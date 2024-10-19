Este política o algoritmo de scheduling Round Robin **"RR"** tiene una idea básica:
En vez de ejecutar procesos hasta completarlos, RR ejecuta un proceso for un tiempo **"time slice"** o **"scheduling quantum"**, para después cambiar a un siguiente proceso en la cola de ejecución. Hace repetidamente esto hasta que todos los procesos están terminados:
1. Ejecuta un proceso de la cola de procesos en runnable.
2. Al llegar una interrupción de tiempo **"[[Hardware Timer's Interrupts]]"**, "deschedula" el proceso.
RR con un time slice razonable es un excelente scheduler si el tiempo de respuesta **"[[Response Time]]"** es la única métrica que nos importa. Si fuera **"[[Turnaround Time]]"** nuestra única métrica, entonces seria la pero de las políticas.

Un ejemplo visual es:
![[Round Robin.png]]
Donde se tienen tres procesos A, B y C donde los tres llegan al 0 ms, y con un Quantum de 1 ms.