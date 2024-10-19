# Lab 3 🚬

## Primera Parte: Estudiando el Planificador de xv6-riscv

### 1.  ¿Qué política de planificación utiliza `xv6-riscv` para elegir el próximo proceso a ejecutarse?
En xv6 se usa la técnica en la que interrupciones de tiempo en hardware "Hardware Timer's Interrupts" manejan el cambio de contexto "Context Switches". Esta política de planificación se llama RoundRobin.

### 2.  ¿Cuáles son los estados en los que un proceso puede permanecer en xv6-riscv y qué los hace cambiar de estado?
Los estados de los procesos son runnable, running, sleeping, zombie, used y unused.
Y están definidos en la estructura de procesos como, p->state == RUNNABLE, RUNNING, SLEEPING, ZOMBIE, UNUSED, USED.

Los cambios de estado son por las siguientes razones:

- Al **crearse un proceso** este se crea con el estado _runnable_.

- Al **planificarse** los proceso se pasan de _runnable_ a _running_.

- Al **esperar una operación** de IO, pasa de _running_ a _sleeping_. Y al finalizar la operación de IO, pasa a _runnable_.

- Al suceder una **interrupción de tiempo** o lo equivalente a que pase el quantum del planificador, el proceso pasa de _running_ a _runnable_.

- Al **finalizar un proceso** este pasa de _running_ a _zombie_ hasta que el proceso padre libera su memoria para pasar al estado _unused_.

### 3.  ¿Qué es un *quantum*? ¿Dónde se define en el código? ¿Cuánto dura un *quantum* en `xv6-riscv`?
Un **Quantum** es el tiempo transcurrido entre los Hardware Timer's Interrupts. 
En xv6 el código se define en `kernel/start.c` específicamente en la función `timerinit()`, acá preguntándole a **CLINT** (core-local interruptor) se obtiene en conjunto con el quantum, donde se generara la interrupción.
Este quantum dura 1/10 de un segundo, o aproximadamente 10ms, según esta definido en la variable `int interval = 1000000`.

### 4. ¿En qué parte del código ocurre el cambio de contexto en `xv6-riscv`? ¿En qué funciones un proceso deja de ser ejecutado? ¿En qué funciones se elige el nuevo proceso a ejecutar?
El cambio de contexto ocurre en `kernel/proc.c` en la función `scheduler()`, al llamarse a `swtch()`.

Las funciones en las que un proceso deja de ser ejecutado son:

- **exit()**, porque sale de un proceso dejándolo en modo `zombie`, y esto puede mientras el proceso estaba en `running`.

- **yield()**, porque "deschedula" un proceso dejándolo en `runnable`.

- **sleep()**, porque pone en estado `sleep` a un proceso, por ejemplo al esperar un IO.

- **kill()**, porque mata al proceso, que puede estar en `running`.

- **wait()**, porque pone a dormir al proceso padre, hasta que el proceso hijo "exits".

Las funciones donde se elige el nuevo proceso a ejecutar son:

- **scheduler()**, porque este elige que proceso se ejecuta.

### 5. ¿El cambio de contexto consume tiempo de un *quantum*?
El cambio de contexto **no** consume tiempo del quantum porque el quantum es el tiempo transcurrido entre timer's interrupts, y los context switches suceden después de un timer interrupt, recién al finalizar la elección del próximo proceso se manda al procesador para ser ejecutado, donde recién al ponerse en ejecución se empieza a contar el quantum.

[Repositorio en GitHub](https://github.com/EmpanadasNqn/Obsidian/tree/main/FaMAF/SistemasOperativos) con resumen hecho en obsidian donde fuimos anotando muchas mas cosas sobre el laboratorio. 