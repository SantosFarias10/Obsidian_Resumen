# Temas
- Spinlocks y zonas criticas
- Implementación de syscalls
- Syscalls exit(), getpid(), fork(), wait(), sleep(), kill(), uptime()
- Manejo de semaphores
- Estrategias de scheduling en general y como se implementarian en xv6
- Conceptos de quantum, ticks, interrupciones y panic
- Preguntas donde les damos resultados similares a los que obtuvieron en el lab 3 y les pedimos que los analicen

# Fuentes
- [OSTEP](https://pages.cs.wisc.edu/~remzi/OSTEP/threads-sema.pdf)
- [Man xv6](https://pdos.csail.mit.edu/6.828/2022/xv6/book-riscv-rev3.pdf)

## Spinlocks y Zonas Criticas
Tenemos la información de que: en xv6 el archivo en kernel/spinlock.c, están las funciones acquire() y release(), que son las que llevan a cabo el "lockeo" y "des-lockeo" de los procesos.

Hacer el "lock" sirve para que en el caso de tener un procesador con múltiples núcleos, no se generen problemas en las zonas criticas.
Específicamente las ***zonas criticas*** son los cambios de contexto, al "deschedular" con yield(), al "schedular" con scheduler(), etc. En general, la zona critica es la que este encerrada entre un acquire() y un release().

## Implementación de Syscalls
Las ***system calls*** son funciones que permiten a procesos del modo usuario acceder a recursos restringidos del modo kernel. Específicamente, evitan que los procesos tengan la posibilidad de manipular el sistema de hardware o el sistema operativo sin control.

En xv6 se implementan en:
1. syscall.h, se les asigna un numero de identificación.
2. syscall.c, para las funciones que manejan la ejecución de las syscalls en sysproc.c, se mapea la identificación de las syscalls a un arreglo.
3. defs.h, se definen los parámetros de entrada de la función???
4. sysproc.c, se define la función.

## Syscalls exit(), getpid(), fork(), wait(), sleep(), kill(), uptime()
- exit(), termina un proceso donde se llamó. Liberando sus recursos y dejándolo en modo zombie hasta que el proceso padre lo elimine por completo.
- getpid(), retorna el process id del proceso donde se llamó.
- fork(), crea un proceso nuevo, quedando padre e hijo. (El hijo recibe cero y el padre el pid del hijo)
- wait(), hace que el padre de un proceso espere hasta que el hijo termine de ejecutarse.
- sleep(), hace que el proceso donde se llamó se bloquee hasta que algo lo "despierte".
- kill(pid), hace un signal kill al proceso con el pid que se le ingreso (elimina un proceso xd).
- uptime(), devuelve el numero de ticks que han pasado desde que el sistema se inició.

## Manejo de Semaphores
Los semaphores son contadores compartidos entre procesos que mantienen la sincronización de los procesos y haciéndolos concurrentes, con capacidad de bloquear y desbloquear procesos. Se utilizan para coordinar las tareas que llevan a cabo los distintos procesos del sistema.

- Cada proceso puede decrementarlo, en el caso especial de decrementarlo cuando esta en cero, este proceso se bloquea y el contador se queda en cero.

- Cada proceso puede incrementarlo, en el caso especial de incrementarlo cuando esta en cero, entonces los procesos bloqueados se desbloquean.

Un ejemplo es el de la función de usuario, pingpong(). Esta al llamarse junto a un numero natural, imprime ping seguido de su respectivo pong la cantidad de veces del numero natural con el que se llamó. 
La funcionalidad de los semaphores acá es la de que no se superpongan los ping o pong mientras se imprimen. Específicamente que siempre este el pong(1) de el ping(1), y no haya el pong(n) de otro ping(n)... con *n > 1*

Las funciones para controlar los semaphores que hicimos son:
- int sem_open(int sem, int value), esta función abre y/o inicializa el semáforo sem con un valor arbitrario value.

- int sem_close(int sem), libera el semáforo sem.

- int sem_up(int sem), incrementa el semáforo sem desbloqueando los procesos cuando su valor es cero.

- int sem_down(int sem), decrementa el semáforo sem bloqueando los procesos cuando su valor es cero. El valor del semáforo nunca puede ser menor a cero.

- int sem_search(int value), busca un semáforo disponible hasta poder entregarlo. De no haber retorna error.

## Estrategias de scheduling en general y como se implementarian en xv6
Utilizamos dos métodos de scheduling en xv6: 
- RoundRobin "RR", es el planificador donde a partir de múltiples procesos en el sistema operativo, se elige y planifica el que haya llegado en un tick mas bajo, para permanecer en ejecución durante un quantum, y al terminarse su quantum desplanificarse y planificarse el siguiente proceso.
  
  Es el scheduler original de xv6, los pasos que sigue son:
  1. Cicla en una lista de procesos.
  2. Si el proceso esta en estado runnable, lo selecciona. Sino, vuelve al paso 1.
  3. Le cambia el estado a running.
  4. Hace el cambio de contexto, ejecutándolo.
  5. vuelve al paso 1.

- Multi-Level Feedback Queue "MLFQ", es el planificador con el mismo funcionamiento que uno RoundRobin, pero con la diferencia de que tiene niveles de prioridades. En el caso de que un proceso en la prioridad mas alta se ejecute, al terminarse su quantum y desplanificarse, se le decrementa la prioridad dejando así los procesos que no se ejecutaron todavía, con una prioridad de ejecución mas alta.
  
  Se implementa en tres funciones, scheduler(), sleep() y yield(), los pasos que sigue son (salteandonos los vistos en RR):
  - scheduler(), Similar a una función que busca máximos, ejecuta el proceso que tenga la prioridad mas alta y de tener la misma prioridad con otro proceso, ejecuta el que haya sido planificado la menor cantidad de veces. Algo a notar, es que se le aumenta ,al proceso ejecutado, la cantidad de veces que fue ejecutado.

  - sleep(), como los procesos que mas utilizan esta función son los Input/Output-bound, se le aumenta la prioridad, ya que al haber sido bloqueados son el caso donde no terminan de utilizar su quantum por completo. Por ende siempre dejándolos mejor parado ante el scheduler.

  - yield(), como esta función es la encargada de "deschedular" los procesos que están siendo ejecutados al terminarse su quantum, es la encargada de decrementarle la prioridad porque es el caso donde un proceso se ejecuto y utilizo todo su quantum.

## Conceptos de quantum, ticks, interrupciones y panic
- Los *quantums* son la cantidad de tiempo asignado a la ejecución de un proceso por el scheduler antes de que suceda el cambio de contexto. En xv6 es la variable interval, encontrada en kernel/start.c.

- Los *ticks* son la cantidad de interrupciones por el timer que sucedieron desde el inicio del sistema. Por ejemplo la syscall uptime().

- Las *interrupciones* son un mecanismo por el cual el hardware interrumpe la ejecución de un proceso para devolverle el control a la CPU. Hay de muchos tipos, por ejemplo las de tiempo, que son las que cuentan los ticks.

- *Panic* es un error fatal en el sistema operativo que provoca que colapse. Al sistema operativo entrar en modo panic, se detiene por completo y/o se reinicia. Estos suceden cuando se detecta una situación critica de la que no se puede recuperar, en nuestro caso con la implementación del planificador, se corrobora constantemente por ejemplo con sched() que no hayan problemas críticos con los procesos.