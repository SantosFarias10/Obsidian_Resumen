En xv6 se usa la técnica en la que interrupciones de tiempo en hardware "[[Hardware Timer's Interrupts]]" manejan el cambio de contexto "Context Switches". Llamada [[RoundRobin]].

## Context Switching

Estos son los pasos que sigue para realizar un cambio de contexto de un proceso a otro:
![[Context Switching.png]]

El Scheduler de xv6 tiene un thread dedicado (saved registers and stack) por CPU. Switching de un thread a otro involucra guardar los registros de los thread's viejos de la CPU y restaurar los registros previamente-guardados del nuevo thread.

La función **swtch** es la que guarda y restaura en un kernel thread switch. Esta función no conoce sobre hilos, simplemente guarda y restaura un set de registros llamados **contexts**.

Cuando es tiempo de que un proceso suelte la CPU, los procesos kernel thread llaman swtch para guardar su contexto y retornar al scheduler context. Cada context esta contenido en una estructura **"struct context"** que esta contenida en una estructura proceso **"struct proc"**  o en una estructura CPU **"struct cpu"**.

Swtch toma dos argumentos "struct context old" y "struct context new". Guarda los registros actuales en old y carga los registros de new, y retorna.
Un ejemplo de esto es al final de un interrupt, tal que **usertrap** llama **[[yield]]** que llama **sched** que llama **swtch** que guarda el contexto actual en **p->context** y cambia al sheduler context previamente guardado en **cpu->scheduler**.

## Scheduling

Examinaremos el switching de un proceso kernel thread a través del scheduler hacia otro proceso. El scheduler existe en una forma conocida como special thread per CPU, cada una corriendo la función **scheduler**. Esta función es la encargada de elegir que proceso le toca ejecutar/correr. Y hace un loop: 
1. Encuentra un proceso para ejecutar/correr.
2. Lo hace correr hasta que hace [[yield]].

El scheduler loops sobre la tabla de procesos "[[Process Table]]" buscando un proceso ejecutable, osea que tenga **p->state == RUNNABLE**. Una vez encuentra uno, setea la variable per-CPU current process **c->proc**, marca el proceso como RUNNING, y llama **swtch** para empezar a ejecutarlo/correrlo.
