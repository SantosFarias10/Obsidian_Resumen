# Temas
- Spinlocks y zonas criticas
- Implementación de syscalls
- Syscalls exit(), getpid(), fork(), wait(), sleep(), kill(), uptime()
- Manejo de semaphores
- Estrategias de scheduling en general y como se implementarian en xv6
- Conceptos de quantum, ticks, interrupciones y panic
- Preguntas donde les damos resultados similares a los que obtuvieron en el lab 3 y les pedimos que los analicen
---
# Fuentes
- [OSTEP](https://pages.cs.wisc.edu/~remzi/OSTEP/threads-sema.pdf)
- [Man xv6](https://pdos.csail.mit.edu/6.828/2022/xv6/book-riscv-rev3.pdf)
---
## Spinlocks y Zonas Criticas
Tenemos la información de que: en xv6 el archivo en `kernel/spinlock.c`, están las funciones `acquire()` y `release()`, que son las que llevan a cabo el ***"lockeo"*** y ***"des-lockeo"*** de los procesos.

Hacer el ***"lock"*** sirve para que en el caso de tener un procesador con múltiples núcleos, no se generen problemas en las ***zonas criticas***.
Específicamente las ***zonas criticas*** son los cambios de contexto, al ***"deschedular"*** con `yield()`, al ***"schedular"*** con `scheduler()`, etc. En general, la ***zona critica*** es la que este encerrada entre un `acquire()` y un `release()`.

- `acquire()`: se utiliza para adquirir un ***spinlock***, que es un ***tipo de bloqueo*** que hace que el hilo que intenta adquirirlo gire en un bucle hasta que el bloqueo esté disponible. Asegura que el hilo actual adquiera el ***spinlock*** de manera segura y atómica, deshabilitando interrupciones y asegurando la correcta ordenación de las operaciones de memoria.

- `release()`: se utiliza para liberar un ***spinlock*** que ha sido previamente adquirido. Asegura que el ***spinlock*** sea liberado de manera segura, verificando que el hilo actual tenga el bloqueo, limpiando la información del CPU, y utilizando una barrera de memoria para asegurar la correcta ordenación de las operaciones de memoria.
---
## Implementación de Syscalls
Las ***system calls*** son funciones que permiten a procesos del ***modo usuario*** acceder a recursos restringidos del ***modo kernel***. Específicamente, ***evitan que los procesos tengan la posibilidad de manipular el sistema*** de hardware o el sistema operativo sin control.

En xv6 se implementan en:
1. `syscall.h`, se les asigna un numero de identificación.
2. `syscall.c`, para las funciones que manejan la ejecución de las syscalls en sysproc.c, se mapea la identificación de las syscalls a un arreglo.
3. `defs.h`, se definen los parámetros de entrada de la función ¿¿¿???
4. `sysproc.c`, se define la función.
---
## Syscalls exit(), getpid(), fork(), wait(), sleep(), kill(), uptime()
- `exit()`, termina un proceso donde se llamó. Liberando sus recursos y ***dejándolo en modo zombie hasta que el proceso padre lo elimine por completo***. Maneja la terminación de un proceso, asegurándose de cerrar todos los recursos asociados y de que el proceso padre sea notificado adecuadamente.
```c
// Salir del proceso actual. No retorna.
// Un proceso que ha salido permanece en el estado zombie
// hasta que su padre llama a wait().
void
exit(int status)
{
  struct proc *p = myproc();

  if(p == initproc)
    panic("init exiting");

  // Cierra todos los archivos abiertos.
  for(int fd = 0; fd < NOFILE; fd++){
    if(p->ofile[fd]){
      struct file *f = p->ofile[fd];
      fileclose(f);
      p->ofile[fd] = 0;
    }
  }

  begin_op();
  iput(p->cwd);
  end_op();
  p->cwd = 0;

  acquire(&wait_lock);

  // Asigna cualquier hijo al proceso init.
  reparent(p);

  // El padre podría estar durmiendo en wait().
  wakeup(p->parent);
  
  acquire(&p->lock);

  p->xstate = status;
  p->state = ZOMBIE;

  release(&wait_lock);

  // Salta al planificador, nunca para volver.
  sched();
  panic("zombie exit");
}
```
- `getpid()`, retorna el process id del proceso donde se llamó.
- `fork()`, crea un proceso nuevo, quedando padre e hijo. (***!!El hijo recibe cero¡¡*** y el padre el pid del hijo). Maneja la creación de un nuevo proceso duplicando el proceso actual, configurando el nuevo proceso hijo para que sea ejecutable y retornando su PID.
```c
// Crear un nuevo proceso duplicando el proceso actual.
// El proceso hijo es una copia del proceso padre.
int
fork(void)
{
  int i, pid;
  struct proc *np;
  struct proc *p = myproc();

  // Asignar un proceso.
  if((np = allocproc()) == 0){
    return -1;
  }

  // Copiar el estado del proceso padre al proceso hijo.
  if((np->pagetable = proc_pagetable(np)) == 0){
    freeproc(np);
    return -1;
  }
  if(uvmcopy(p->pagetable, np->pagetable, p->sz) < 0){
    freeproc(np);
    return -1;
  }
  np->sz = p->sz;

  // Copiar el contexto de ejecución del proceso padre al proceso hijo.
  *(np->trapframe) = *(p->trapframe);

  // Configurar el valor de retorno del proceso hijo a 0.
  np->trapframe->a0 = 0;

  // Copiar los archivos abiertos del proceso padre al proceso hijo.
  for(i = 0; i < NOFILE; i++)
    if(p->ofile[i])
      np->ofile[i] = filedup(p->ofile[i]);
  np->cwd = idup(p->cwd);

  safestrcpy(np->name, p->name, sizeof(p->name));
  pid = np->pid;

  // Añadir el proceso hijo a la lista de procesos.
  acquire(&wait_lock);
  np->parent = p;
  release(&wait_lock);

  acquire(&np->lock);
  np->state = RUNNABLE;
  release(&np->lock);

  return pid;
}
```
- `wait()`, ***hace que el padre de un proceso espere hasta que el hijo termine de ejecutarse***.  Retornando el PID del hijo terminado o `-1` si no tiene hijos.
```c
// Espera a que un proceso hijo salga y retorna su pid.
// Retorna -1 si este proceso no tiene hijos.
int
wait(uint64 addr)
{
  struct proc *pp;
  int havekids, pid;
  struct proc *p = myproc();

  acquire(&wait_lock);

  for(;;){
    // Escanear la tabla de procesos buscando hijos.
    havekids = 0;
    for(pp = proc; pp < &proc[NPROC]; pp++){
      if(pp->parent != p)
        continue;
      havekids = 1;
      if(pp->state == ZOMBIE){
        // Encontrado un hijo zombie.
        pid = pp->pid;
        if(addr != 0 && copyout(p->pagetable, addr, (char *)&pp->xstate, sizeof(pp->xstate)) < 0){
          release(&wait_lock);
          return -1;
        }
        freeproc(pp);
        release(&wait_lock);
        return pid;
      }
    }

    // No hay hijos, salir.
    if(!havekids || p->killed){
      release(&wait_lock);
      return -1;
    }

    // Esperar a que los hijos cambien de estado.
    sleep(p, &wait_lock);
  }
}
```
- `sleep()`, hace que el proceso donde se llamó se bloquee hasta que algo lo "despierte". Es utilizada para poner un proceso a dormir de manera segura, asegurando que no se pierdan eventos de despertar mientras el proceso está dormido.
```c
// Libera el bloqueo atómicamente y duerme en chan.
// Vuelve a adquirir el bloqueo cuando se despierta.
void
sleep(void *chan, struct spinlock *lk)
{
  struct proc *p = myproc();
  
  // Debe adquirir p->lock para
  // cambiar p->state y luego llamar a sched.
  // Una vez que tenemos p->lock, podemos estar
  // seguros de que no perderemos ningún wakeup
  // (wakeup bloquea p->lock),
  // por lo que está bien liberar lk.

  acquire(&p->lock);  //DOC: sleeplock1
  release(lk);

  // Ir a dormir.
  p->chan = chan;
  p->state = SLEEPING;

  sched();

  // Limpiar.
  p->chan = 0;

  // Volver a adquirir el bloqueo original.
  release(&p->lock);
  acquire(lk);
}
```
- `kill(pid)`, hace un signal kill al proceso con el pid que se le ingreso (elimina un proceso xd). Se utiliza para marcar un proceso para que sea terminado. El proceso no se terminará inmediatamente, sino que se marcará para ser terminado la próxima vez que intente regresar al espacio de usuario.
```c
// Mata el proceso con el pid dado.
// La víctima no saldrá hasta que intente regresar
// al espacio de usuario (ver usertrap() en trap.c).
int
kill(int pid)
{
  struct proc *p;

  for(p = proc; p < &proc[NPROC]; p++){
    acquire(&p->lock);
    if(p->pid == pid){
      p->killed = 1;
      if(p->state == SLEEPING){
        // Despierta el proceso de sleep().
        p->state = RUNNABLE;
      }
      release(&p->lock);
      return 0;
    }
    release(&p->lock);
  }
  return -1;
}
```
- `uptime()`, devuelve el numero de ticks que han pasado desde que el sistema se inició.
---
## Manejo de Semaphores
Los ***semaphores*** son contadores compartidos entre procesos que mantienen la sincronización de los procesos y haciéndolos concurrentes, con capacidad de ***bloquear*** y ***desbloquear*** procesos. Se utilizan para coordinar las tareas que llevan a cabo los distintos procesos del sistema.
- Cada proceso puede decrementarlo, en el caso especial de decrementarlo cuando esta en cero, este proceso se bloquea y el contador se queda en cero.
- Cada proceso puede incrementarlo, en el caso especial de incrementarlo cuando esta en cero, entonces los procesos bloqueados se desbloquean.

Un ***ejemplo*** es el de la función de usuario, `pingpong()`. Esta al llamarse junto a un numero natural, imprime ping seguido de su respectivo pong la cantidad de veces del numero natural con el que se llamó.
La ***funcionalidad*** de los semaphores acá es la de que no se superpongan los ping o pong mientras se imprimen. Específicamente que siempre este el pong(1) de el ping(1), y no haya el pong(n) de otro ping(n)... con *n > 1*

El `semaphore` esta especificado de la siguiente manera:
```c
struct semaphore {
  int value;
  struct spinlock lock;
};
```
donde:
- `value`: Representa el valor del semáforo. Este valor se utiliza para controlar el acceso a un recurso compartido. Un valor positivo indica la cantidad de recursos disponibles, mientras que un ***valor negativo o cero indica que no hay recursos disponibles o que el semáforo está cerrado***.
- `lock`: Es spinlock que se utiliza para asegurar que las operaciones sobre el semáforo sean ***atómicas***. Esto significa que cuando un hilo está modificando el valor del semáforo, otros hilos no pueden acceder a él hasta que el bloqueo se libere.

Las funciones para controlar los semaphores que hicimos son:
- `int sem_open(int sem, int value)`, esta función abre y/o inicializa el semáforo sem con un valor arbitrario value. Si el semáforo es válido y no está en uso, lo abre con el valor especificado. Retorna `1` si tiene éxito y `0` si falla.
```c
int sem_open(int sem, int value) {
  if (sem < 0 || sem >= MAX_SEMAPHORES || semaforo[sem].value != -1 || 
  value < 0) {       // SI sem es invalido
    return 0;        // Retornamos cero que es el error
  }

  acquire(&(semaforo[sem].lock));      // Bloqueamos
  semaforo[sem].value = value;         // Le asignamos el valor arbitrario
  release(&(semaforo[sem].lock));      // Desbloqueamos

  return 1;             // Retornamos 1 que es exito
}
```
- `int sem_close(int sem)`, libera el semáforo sem.  Si el semáforo es válido, lo cierra estableciendo su valor en `-1`. Retorna `1` si tiene éxito y `0` si falla.
```c
int sem_close(int sem) {
  if (sem < 0 || sem >= MAX_SEMAPHORES) {  // SI sem es invalido
    return 0;     // El semaphore es invalido Retornamos 0 (o sea el error)
  }
  
  acquire(&(semaforo[sem].lock));   // Bloqueamos
  semaforo[sem].value = -1;      //Le asignamos el valor -1 (o sea ¿no esta en uso?)
  release(&(semaforo[sem].lock));   // Desbloqueamos

  return 1;   // Retornamos 1 (exito)
}
```
- `int sem_up(int sem)`, incrementa el semáforo sem desbloqueando los procesos cuando su valor es cero.
```c
int sem_up(int sem) {
  if (sem < 0 || sem >= MAX_SEMAPHORES || semaforo[sem].value == -1) {
    return 0;  // Retornamos 0 (error) si sem es invalido
  }
  acquire(&(semaforo[sem].lock));  // Bloqueamos
  
  if(semaforo[sem].value == 0){  // Si el valor del semáforo es 0, se despiertan los procesos que están esperando en este semáforo.
    wakeup(&(semaforo[sem])); //Despertamos el proceso
  }
  
  (semaforo[sem].value)++; //Incrementamos el valor
  
  release(&(semaforo[sem].lock));  // Desbloqueamos

  return 1;  // Retornamos 1 (exito)
}
```
- `int sem_down(int sem)`, decrementa el semáforo sem bloqueando los procesos cuando su valor es cero. El valor del semáforo nunca puede ser menor a cero.
```c
int sem_down(int sem) {
  if (sem < 0 || sem >= MAX_SEMAPHORES || semaforo[sem].value == -1) {
    return 0;  // Retornamos 0 (error) si sem es invalido
  }

  acquire(&(semaforo[sem].lock));  // Bloqueamos

  if(semaforo[sem].value == 0){
    sleep(&(semaforo[sem]), &(semaforo[sem].lock));  // Si el valor del semáforo es 0, el proceso se pone a dormir hasta que el semáforo esté disponible.
  }

  semaforo[sem].value--;  // Decrementamos el valor

  release(&(semaforo[sem].lock));  // Desbloqueamos
  
  return 1;  // Retornamos exito
}
```
- `int sem_search(int value)`, busca un semáforo disponible hasta poder entregarlo. De no haber retorna error.
```c
//Busca un semaforo disponible (semaforo que no este cerrado)
int sem_search(int value) {
  int sem = 0;

  while(sem < MAX_SEMAPHORES && sem_open(sem, value) == 0) { //Va a salir si sem es menos al max de semaforos y si logra abrirlo
    sem++;
  }

  if(sem == MAX_SEMAPHORES) {
    return -1;
  }

  return sem;  // Si se encuentra un semáforo disponible, se retorna su índice.
}
```
- `void sem_init()`, función para inicializar los semáforos en -1 se llama al principio del main.
```c
void sem_init() {
  for (int i = 0; i < MAX_SEMAPHORES; i++) {

    semaforo[i].value = -1; //Inicializamos todos los sem en -1 
                            //("no existen"/"no estan en uso")

    initlock(&(semaforo[i].lock), "Semaphore"); //Inicializamos el spinlock y le damos nombre de Semaphore (hay que ponerlo en sem_init junto con la asignacion del -1 porque ahi se inicializa el semaforo)
  }
}
```
---
## Pingpong.c
```c
#include "kernel/types.h"
#include "kernel/stat.h"
#include "user/user.h"

int main(int argc, char **argv) {
    if(argc != 2) {  // comprobamos que en consola hayan pasado un argumento
        printf("ERROR: el programa debe recibir un argumento\n");
        return 0;
    }

    int length = atoi(argv[1]);  // atoi transforma un string en un int
    if(length < 1) { // Comprobamos que el argumento que paso el usuario sea mayor que 1
        printf("ERROR: el argumento debe ser un numero entero mayor a cero\n");
        return 0;
    }    

    int sem_hijo = sem_search(0);
    int sem_padre = sem_search(1);
    // inicializamos con la funcion sem_search con los valores 0 y 1 respectivamente porque en el fork el caso hijo es cuando (fork == 0)

    int f = fork(); // creamos un proceso hijo

    if(f == -1) { // Si es que el fork falla
        printf("ERROR: fallo el fork\n"); // Imprimimos un mensaje de error
        return 0;
    } else if(f == 0) { //caso del hijo
        for (unsigned int i = 0; i < length; i++) { 
          sem_down(sem_hijo);  // Utiliza sem_down(sem_hijo) para esperar a que                                 el semáforo del hijo esté disponible
          printf("\tpong\n");  // Imprimimos "pong"
          sem_up(sem_padre);   // Utiliza sem_up(sem_padre) para señalar al                                     semáforo del padre que puede continuar.
        }
        return 1;
    } else {            //caso del padre
        for (unsigned int i = 0; i < length; i++) {
          sem_down(sem_padre);  // Utiliza sem_down(sem_padre) para esperar a                                    que el semáforo del padre esté disponible.
          printf("ping\n");  // Imprimimos "ping"
          sem_up(sem_hijo);  // Utiliza sem_up(sem_hijo) para señalar al                                      semáforo del hijo que puede continuar.
        }
    }
    
    wait(0); //esperamos a que el hijo termine
    sem_close(sem_hijo);  // Cerramos el semaforo hijo
    sem_close(sem_padre); // Cerramos el semaforo padre

    return 1;
}
```

## Estrategias de scheduling en general y como se implementarian en xv6
Utilizamos dos métodos de scheduling en xv6: 
- ***RoundRobin (RR)***, es el planificador donde a partir de múltiples procesos en el sistema operativo, se elige y planifica el que haya llegado en un tick mas bajo, para permanecer en ejecución durante un quantum, y al terminarse su quantum desplanificarse y planificarse el siguiente proceso.
  
  Es el scheduler original de xv6, los pasos que sigue son:
  1. Cicla en una lista de procesos.
  2. Si el proceso esta en estado runnable, lo selecciona. Sino, vuelve al paso 1.
  3. Le cambia el estado a running.
  4. Hace el ***cambio de contexto***, ejecutándolo.
  5. vuelve al paso 1.

- `scheduler()` con RR:
```c
// Planificador de procesos por CPU.
// Cada CPU llama a scheduler() después de configurarse.
// Scheduler nunca regresa. En su lugar, entra en un bucle haciendo:
//  - elegir un proceso para ejecutar.
//  - cambiar a ejecutar ese proceso.
//  - eventualmente ese proceso transfiere el control
//    a través de swtch de vuelta al planificador.
void
scheduler(void)
{
  struct proc *p;
  struct cpu *c = mycpu();
  
  c->proc = 0;
  for(;;){
    // Evitar deadlock asegurando que los dispositivos puedan interrumpir.
    intr_on();

    for(p = proc; p < &proc[NPROC]; p++) {
      acquire(&p->lock);
      if(p->state == RUNNABLE) {
        // Cambiar al proceso elegido. Es trabajo del proceso
        // liberar su bloqueo y luego volver a adquirirlo
        // antes de regresar a nosotros.
        p->state = RUNNING;
        c->proc = p;

        p->scheduled++;

        swtch(&c->context, &p->context);

        // El proceso ha terminado de ejecutarse por ahora.
        // Debería haber cambiado su p->state antes de regresar.
        c->proc = 0;
      }
      release(&p->lock);
    }
  }
}
```

- ***Multi-Level Feedback Queue (MLFQ)***, es el planificador con el mismo funcionamiento que uno RoundRobin, pero con la diferencia de que tiene niveles de prioridades. En el caso de que un proceso en la prioridad mas alta se ejecute, al terminarse su quantum y desplanificarse, se le decrementa la prioridad dejando así los procesos que no se ejecutaron todavía, con una prioridad de ejecución mas alta.
  
  Se implementa en tres funciones, `scheduler()`, `sleep()` y `yield()`, los pasos que sigue son (salteandonos los vistos en RR):
  - `scheduler()`, Similar a una función que busca máximos, ejecuta el proceso que tenga la prioridad mas alta y de tener la misma prioridad con otro proceso, ejecuta el que haya sido planificado la menor cantidad de veces. Algo a notar, es que se le aumenta ,al proceso ejecutado, la cantidad de veces que fue ejecutado.
```c
// Planificador de procesos por CPU.
// Cada CPU llama a scheduler() después de configurarse.
// Scheduler nunca regresa. En su lugar, entra en un bucle haciendo:
//  - elegir un proceso para ejecutar.
//  - cambiar a ejecutar ese proceso.
//  - eventualmente ese proceso transfiere el control
//    a través de swtch de vuelta al planificador.
void
scheduler(void)
{
  struct proc *a;
  struct proc *b;
  struct cpu *c = mycpu();
  
  c->proc = 0;
  for(;;){
    // Evitar deadlock asegurando que los dispositivos puedan interrumpir.
    intr_on();

	// Cuarta parte
	b = 0;

    for(a = proc; a < &proc[NPROC]; a++) {
      acquire(&a->lock);
      if(a->state == RUNNABLE) {

		// Cuarta Parte
		// MLFQ Regla 3: Si el proceso A tiene mayor prioridad que el proceso B,
		// Corre A. (y no B)
		// MLFQ regla 4: Si dos procesos A y B tienen la misma prioridad, corre
		// el que menos veces fue elegido por el planificador.
		if (b == 0 ||
		   (a->priority > b->priority ||)
		   (a->priority == b->priority && a->scheduled < b->scheduled)){

		   if(b != 0){
			   release(&b->lock);
		   }
		   b = a;
		} else { release(&a->lock) }
	  } else { release(&a->lock) }
	}

	if(b != 0 && b->state == RUNNABLE){
	    // Cambiar al proceso elegido. Es trabajo del proceso
        // liberar su bloqueo y luego volver a adquirirlo
        // antes de regresar a nosotros.
        b->state = RUNNING;
        c->proc = p;
        
        //Lo que ante era parte tres
        b->scheduled++;
                
        swtch(&c->context, &b->context);

        // El proceso ha terminado de ejecutarse por ahora.
        // Debería haber cambiado su p->state antes de regresar.
        c->proc = 0;
	    release(&b->lock);
    }
  }
}
```
  - `sleep()`, como los procesos que mas utilizan esta función son los Input/Output-bound, se le aumenta la prioridad, ya que al haber sido bloqueados son el caso donde no terminan de utilizar su quantum por completo. Por ende siempre dejándolos mejor parado ante el scheduler.
```c
// Libera el bloqueo atómicamente y duerme en chan.
// Vuelve a adquirir el bloqueo cuando se despierta.
void
sleep(void *chan, struct spinlock *lk)
{
  struct proc *p = myproc();
  
  // Debe adquirir p->lock para
  // cambiar p->state y luego llamar a sched.
  // Una vez que tenemos p->lock, podemos estar
  // seguros de que no perderemos ningún wakeup
  // (wakeup bloquea p->lock),
  // por lo que está bien liberar lk.

  acquire(&p->lock);  //DOC: sleeplock1
  release(lk);

  // Ir a dormir.
  p->chan = chan;
  p->state = SLEEPING;

  //Tercera Parte
  //Le aumentamos la prioridad si es que no esta ya en la mas alta,
  //ya que en teoria no completo el quantum.
  //Esto porque los unicos procesos que no usan el quantum completo son los
  //IOBOUND. Que serian los unicos en llamar a sleep.
  //MLFQ regla 4: Descender de prioridad cada vez que el proceso pasa todo un
  //quantum realizando cómputo. Ascender de prioridad cada vez que el proceso
  //se bloquea antes de terminar su quantum.
  if (p->priority < NPRIO - 1) {
    p->priority++;
  }
  
  sched();

  // Limpiar.
  p->chan = 0;

  // Volver a adquirir el bloqueo original.
  release(&p->lock);
  acquire(lk);
}
```
  - `yield()`, como esta función es la encargada de ***"deschedular"*** los procesos que están siendo ejecutados al terminarse su quantum, es la encargada de decrementarle la prioridad porque es el caso donde un proceso se ejecuto y utilizo todo su quantum.
```c
// Cede la CPU por una ronda de planificación.
void
yield(void)
{
  struct proc *p = myproc();
  acquire(&p->lock);
  p->state = RUNNABLE;

  //Tercera Parte
  //Le bajamos la prioridad si no esta ya en la ultima, porque fue planificado.
  //MLFQ regla 2: Si dos procesos A y B tienen la misma prioridad, corre el que
  //menos veces fue elegido por el planificador.
  if(p->priority > 0) {
    p->priority--;
  }

  sched();
  release(&p->lock);
}
```
---
## Conceptos de quantum, ticks, interrupciones, panic, Context Switches
- Los ***quantums*** son la cantidad de tiempo asignado a la ejecución de un proceso por el ***scheduler*** antes de que suceda el cambio de contexto. En xv6 es la variable `interval`, encontrada en `kernel/start.c`.
- Los ***ticks*** son la cantidad de interrupciones por el timer que sucedieron desde el inicio del sistema. Por ejemplo la syscall `uptime()`.
- Las ***interrupciones*** son un mecanismo por el cual el hardware interrumpe la ejecución de un proceso para devolverle el control a la CPU. Hay de muchos tipos, por ejemplo las de tiempo, que son las que cuentan los ticks.
- ***Panic*** es un error fatal en el sistema operativo que provoca que colapse. Al sistema operativo entrar en modo panic, se detiene por completo y/o se reinicia. Estos suceden cuando se detecta una situación critica de la que no se puede recuperar, en nuestro caso con la implementación del planificador, se corrobora constantemente por ejemplo con `sched()` que no hayan problemas críticos con los procesos.
- ***Context Switches*** es proceso mediante el cual un sistema operativo guarda el estado de un proceso o hilo y lo restaura posteriormente. Esto permite que múltiples procesos compartan una única CPU y es esencial para la multitarea en sistemas operativos modernos. Durante un cambio de contexto, el sistema operativo guarda el estado del proceso actual (como registros de CPU, punteros de pila, etc.) y carga el estado del siguiente proceso a ejecutar.
---
## Preguntas del lab3

#### Primera parte
1. ¿Qué política de planificación utiliza `xv6-riscv` para elegir el próximo proceso a ejecutarse?
En xv6 se usa la técnica en la que interrupciones de tiempo en hardware ***"Hardware Timer's Interrupts"*** que manejan el cambio de contexto "Context Switches". Esta política de planificación se llama ***RoundRobin***.

 2. ¿Cuáles son los estados en los que un proceso puede permanecer en `xv6-riscv` y qué los hace cambiar de estado?
Los estados de los procesos son ***runnable***, ***running***, ***sleeping***, ***zombie***, ***used*** y ***unused***.
Y están definidos en la estructura de procesos como, `p->state == RUNNABLE, RUNNING, SLEEPING, ZOMBIE, UNUSED, USED`.
Los ***cambios de estado*** ocurren por las siguientes razones:
- Al ***crearse un proceso*** se crea con el estado `runnable`.
- Al ***planificarse***, los procesos pasan de _runnable_ a `running`.
- Al ***esperar una operación*** de IO, pasa de `running` a `sleeping` (ya que se "bloquea"). Y al finalizar la operación de IO, pasa a `runnable`.
- Al suceder una ***interrupción de tiempo*** o lo equivalente a que ***pase el quantum*** del planificador, el proceso pasa de `running` a `runnable`.
- Al ***finalizar un proceso***, este pasa de `running` a `zombie` hasta que el proceso padre libera su memoria para pasar al estado `unused`.

 3. ¿Qué es un ***quantum***? ¿Dónde se define en el código? ¿Cuánto dura un ***quantum*** en `xv6-riscv`?
Un **Quantum** es el tiempo transcurrido entre los Hardware Timer's Interrupts.
En `xv6` el código se define en `kernel/start.c` específicamente en la función `timerinit()`, acá preguntándole a **CLINT** (core-local interruptor) se obtiene en conjunto con el ***quantum***, donde se generara la interrupción.
Este quantum dura ***1/10 de un segundo***, o aproximadamente 10ms, según está definido en la variable `int interval = 1000000`.

 4. ¿En qué parte del código ocurre el ***cambio de contexto*** en `xv6-riscv`? ¿En qué funciones un proceso deja de ser ejecutado? ¿En qué funciones se elige el nuevo proceso a ejecutar?
El ***cambio de contexto*** ocurre en `kernel/proc.c`, en la función `sched()` y `scheduler()`, al llamarse a `swtch()`.
Las funciones en las que un proceso deja de ser ejecutado son:
- ***exit()***, porque sale de un proceso dejándolo en modo `zombie`, esto puede suceder mientras el proceso estaba en `running`.
- ***yield()***, porque ***"deschedula"*** un proceso dejándolo en `runnable`.
- ***sleep()***, porque pone en estado `sleep` a un proceso, por ejemplo al esperar un IO.
- ***kill()***, porque mata al proceso, que puede estar en `running`.
- ***wait()***, porque pone a dormir al proceso padre, hasta que el proceso hijo "exits".

Las función donde se elige el nuevo proceso a ejecutar es:
- ***scheduler()***, porque este elige qué proceso se ejecuta.

 5. ¿El ***cambio de contexto consume tiempo de un quantum***?
El ***cambio de contexto*** ocurre al principio del ***quantum***. Esto afecta muy levemente al tiempo real que usa un proceso, ya que el ***cambio de contexto consume muy poco tiempo***, y por ende ***muy poco tiempo del quantum es dedicado al cambio de contexto***; el tiempo real que se usa para el proceso en cada quantum sería de (quantum_time - cambioDeContexto_time).
Esto significa que puede haber casos donde, si el quantum es menor al tiempo que necesita el cambio de contexto, se gastaría todo en el cambio de contexto y no podría ejecutar el proceso.

---
#### Segunda parte
##### Experimento 1: ¿Cómo son planificados los programas iobound y cpubound?

2. ¿Los procesos se ejecutan en ***paralelo***? ¿En promedio, qué proceso o procesos se ejecutan primero? Hacer una observación cualitativa.
**Sí**, se puede decir que se ejecutan en paralelo, ***si consideramos que ejecutar en paralelo es que se ejecuten intercaladamente los procesos***. Como estamos emulando QEMU con un solo núcleo, el planificador ejecuta un proceso hasta que se le acaba el quantum, para recién ahí ejecutar el siguiente proceso. Es decir, el planificador no espera a que finalice un proceso para ejecutar el siguiente.
El planificador sigue las reglas de RoundRobin, por lo que ejecuta el primero en llegar o más específicamente, el primero que se llamó, ej: `iobench 10 &; cpubench 10 &; cpubench 10 &; cpubench 10 &`; En este caso se planifica primero el iobench, seguido de los otros tres cpubench, pero se terminan de ejecutar los cpubench's antes.
Entonces, en promedio (***casi siempre en nuestro caso***), se terminan de ejecutar primero los cpubench, ejecutando los iobench recién cuando se completan los cpubench. Creemos que esto se da porque los iobench al realizar operaciones de I/O, el planificador no los tiene en cuenta si están bloqueados, de esa forma siempre planifica a los de cpubench, además al realizar una cantidad mucho mayor de operaciones, solicitan en mucha mayor cantidad la cpu por lo que es más probable que se planifiquen.

3. ¿Cambia el ***rendimiento de los procesos iobound*** con respecto a la cantidad y tipo de procesos que se estén ejecutando en paralelo? ¿Por qué?
Si se ejecuta ***un iobench solo***, desperdicia ticks durante su ejecución usando más de los que necesita, esto se da porque está bloqueado, esperando la operación I/O, alguna de las veces que el planificador busca procesos para ejecutar.
Si se ejecutan ***dos iobench*** cada uno tarda una menor cantidad de ticks en finalizar, por lo que es más óptimo en cuestión de tiempo ya que el planificador va cambiando entre ambos procesos a la hora de elegir cuál planificar.
Si se va ***aumentando la cantidad de iobenchs*** la cantidad de ticks empieza a aumentar dependiendo de la cantidad de procesos ejecutándose en paralelo.
Si se ***mezclan iobench y cpubench***, la primera vez que se ejecuta un proceso de iobench le lleva una gran cantidad de ticks terminar de ejecutarse, ya que se empieza a ejecutar cuando es llamado, pero termina luego de que finalice la ejecución de todos los procesos cpubench, por lo que afecta mucho el rendimiento de cada primer llamada de iobench, pero luego, como para ejecutar los iobench se espera a que terminen todos los cpubench, se comportan como si solo hubiera iobenchs.

4. ¿Cambia el ***rendimiento de los procesos cpubound*** con respecto a la cantidad y tipo de procesos que se estén ejecutando en paralelo? ¿Por qué?
Sí, cambia el rendimiento de los procesos cpubound.
Con respecto a la cantidad, mientras más cpubounds haya, más tarda en finalizar los procesos, debido a que se planifican los otros procesos cpubound.
Con respecto al tipo de procesos que se ejecutan en paralelo, de ser los iobench, estos ocupan ticks por lo que se extiende el tiempo de ejecución de los cpubound, bajando su rendimiento, pero no tanto, ya que al estar en el estado de bloqueado, el planificador no los tiene en cuenta.

5. ¿Es adecuado ***comparar la cantidad de operaciones de cpubound con la cantidad de operaciones iobound***?
Con nuestra métrica, sí es posible, ya que nosotros no vemos el tipo de las operaciones, si no la cantidad de las mismas. Al principio pensamos en poner el promedio de operaciones que hace el proceso cada 10ms, y si el proceso tiene un tiempo de ejecución más grande, su promedio bajaría. Luego, decidimos simplemente hacer que las métricas de Iobench y Cpubench tengan la misma cantidad de dígitos para simplificar a la vista y por ende la comparación. De todas formas, comparar en sí el resultado que nos da la métrica, no tiene mucho sentido ya que no nos dice nada.

---
##### Experimento 2: ¿Qué sucede cuando cambiamos el largo del quantum?

1. ¿Fue necesario modificar las métricas para que los resultados fueran comparables? ¿Por qué?
Sí, fue necesario modificar las métricas para que los resultados vuelvan a ser comparables. Ya que al ser más chico el quantum, los procesos realizaban más operaciones por quantum y por ende la métrica anterior quedaba muy chica.

2. ¿Qué cambios se observan con respecto al experimento anterior? ¿Qué comportamientos se mantienen iguales?
	- ***Quantum 10mil***: Con respecto al experimento anterior, el quantum, al ser más chico, el planificador tiene más probabilidad de ejecutar un proceso iobound y por lo tanto hay más probabilidades de que termine un proceso iobound antes de que terminen todos los procesos cpubound, cosa que casi nunca pasaba con el quantum de 100mil.
	- ***Quantum mil***: Con este quantum, lo dicho anteriormente se amplifica más, ya que ahora la probabilidad de que el planificador ejecute un proceso iobound o uno cpubound, son casi iguales, por lo que finalizan los procesos casi que por orden de llegada. Específicamente lo que pasa es que en los procesos iobound, al ponerse en bloqueado esperando a las acciones I/O, hay más probabilidad de que el planificador los agarre cuando dejaron de estar bloqueados. Lo mismo pasa con el quantum de 10mil, pero con menor frecuencia.

3. ¿Con un quantum más pequeño, se ven beneficiados los procesos iobound o los procesos cpubound?
Si el quantum es más chico, los procesos que se benefician más son los iobound debido a que ya no tienen a esperar a que los procesos cpubound finalicen porque tienen mayor probabilidad de ser elegidos por el planificador al haber mayor cantidad de interrupciones, esto hace que los procesos iobound tengan un tiempo de respuesta más chico. En consecuencia, como ahora todos los procesos usan la cpu casi igualitariamente, la cantidad de ticks que les lleva terminar de ejecutarse aumenta en promedio para todos los procesos.🚬

---
#### MLFQ
- ¿Se puede producir ***starvation*** en el nuevo planificador?
Sí ­puede haber starvation, ya que los procesos que quedan en la cola de prioridades más baja puede que les lleve mucho más tiempo de lo que deberían en ejecutarse si hubiera muchos procesos en las colas de prioridades altas. En nuestro caso, los procesos pueden sufrir starvation, puesto que en ningún momento aplicamos la regla 5 del planificador MLFQ, que hace que los procesos suban a la prioridad más alta cada cierto tiempo. Los que más deberían sufrir de starvation serían los procesos cpubound ya que, con nuestra regla, cada vez que se desplanifique por un time interrupt, su prioridad será disminuida. Los procesos iobound tendrán la prioridad más alta al bloquearse más seguido antes de un time interrupt, por lo que mientras más procesos iobound haya, los procesos cpubound tardarán más en ejecutarse.

- Anotaciones acerca de la cuarta parte:
La modificación para la cuarta parte se hace en el scheduler, el cual, básicamente, recorre el arreglo de procesos en busca del próximo proceso a ejecutar según las nuevas reglas.
Una vez hecha la implementación, vimos que los procesos cpubound seguían terminando todos antes que los procesos de iobound, lo que pensábamos que era incorrecto dado que los iobound tienen prioridad más alta que los cpubound, pero después de varias pruebas, modificaciones al código, preguntas e investigaciones llegamos a la conclusión de que ese comportamiento no es incorrecto, el planificador funciona correctamente ya que durante la ejecución de todos los procesos, los iobound se planifican con mayor frecuencia, por lo que podemos concluir que efectivamente se cumple la regla de elegir al proceso que tiene mayor prioridad para ejecutar.

- Diferencias con Round Robin:
Las principales diferencias que pudimos observar con el quantum de 100.000 fueron que con el planificador mlfq, los iobound se planifican con una frecuencia distinta (en comparación con el Round Robin) dependiendo de la cantidad de procesos cpubound con los que se ejecute en paralelo. Por los experimentos realizados con el quantum de 100.000 podemos observar que mientras mayor sea la cantidad de procesos cpubound en comparación con la cantidad de procesos iobound, mayor será la frecuencia de planificación de los procesos iobound. En cambio, si la cantidad de procesos cpubound es mas o menos igual que la cantidad de procesos iobound, la frecuencia de planificación de ambos tipos de procesos se vuelve mas errática, puede variar desde que la cantidad de planificaciones sea muy similar, como se puede ver en el caso de ejecutar "iobench 10 &; cpubench 10 &", o puede tener un comportamiento tan aleatorio como tener procesos cpubench que tengan menor cantidad de planificaciones que un iobench y otros que tengan una cantidad mayor; otro caso posible es que haya mucho mas procesos iobench que cpubench, en el cual nos enfrentaríamos con la situación de que el cpubench (en varias ocasiones) sea planificado más veces que los iobenchs.
---