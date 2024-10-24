Para virtualizar la CPU, el SO necesita una forma de compartir la CPU física entre los muchos trabajos que parecieran que que corren al mismo tiempo. La idea es simple: correr un proceso por un ratito, luego correr otro y así sucesivamente. Compartiendo el tiempo de CPU de esta manera se virtualiza la CPU 

## Una técnica básica: Ejecución Directa Limitada
La parte de la idea "***Ejecución Directa***" es simple: solo hay que correr el programa directamente en la CPU. Así, cuando el SO desee comenzar a ejecutar un programa, solo crea una entrada para él en la lista de procesos, le asigna un poco de memoria, carga el código del programa en memoria (desde el disco), ubica su punto de entrada (o sea, la rutina `main()`), salta a ella, y comienza a correr el código del usuario.

![[Figure 6.1: Protocolo de la Ejecuci  ́on Directa (Sin L ́ımites).png]]
Figure 6.1: Protocolo de la Ejecución Directa (Sin Limites)

#### Problema
Este enfoque genera un par de problemas al intentar virtualizar la CPU:
- Si solo corremos un programa, ¿Como puede el SO asegurar que dicho programa no hace nada que no queremos que haga, y que todavía corra eficientemente?
- Cuando estamos corriendo un proceso, ¿Como lo detiene el SO y cambia a otro proceso, así implementando el ***Tiempo Compartido*** que se requiere virtualizar la CPU?

## Problema 1: Operaciones Restringidas
La ejecución directa limitada es rápida; el programa corre nativamente en el hardware de la CPU y por lo tanto ejecuta tan rápido. Pero correr en la CPU genera un problema: ¿Que pasa si el proceso desea realizar algún tipo de operacion restringida?Por ejemplo: hacer un pedido de I/O a un disco, u obtener acceso a mas recursos del sistema como CPU o memoria.
Un enfoque seria dejar que cualquier proceso haga lo que quiera en términos de I/O y otras operaciones restringidas, pero hacerlo evitaría la construcción de muchos tipos de sistemas que son deseables (por ejemplo: construir un sistema de archivos que verifique los permisos antes de otorgar acceso aun archivo). Si dejamos que cualquier usuario emita I/O al disco, un proceso podría simplemente leer o escribir el disco entero y así todas las protecciones se perderían.
Por lo tanto, introducimos un nuevo modo de procesador, conocido como ***modo Usuario***; se restringe en lo que puede hacer, por ejemplo: no puede emitir solicitudes de I/O, si lo hace llevaría a que el procesador genere una excepción y el SO mataría el proceso.
En contraste esta el ***modo Kernel***, en el que el SO (o kernel) corre: El código que corre hace lo que le pinte, incluyendo ***operaciones privilegiadas***, por ejemplo: emitir solicitudes de I/O y ejecutar instrucciones restringidas.

#### ¿Que pasa si el usuario quiere realizar una operacion restringida?
Todo el hardware moderno les proporciona a los programas de usuario la capacidad de que realicen una ***llamada al sistema*** (system call): permiten al kernel dar pedacitos de funcionalidad a los programas de usuario, por ejemplo: acceder al sistema de archivos, crear y destruir procesos, comunicarse con otros procesos, y asignar mas memoria.

#### Y ¿Como se ejecuta una syscall?
El programa ejecuta una instrucción ***Trap***: salta al kernel, o sea eleva el privilegio a kernel mode, por lo tanto ahora puede hacer cualquier operacion privilegiada. Cuando finaliza, el SO llama a a otra instrucción ***Retorno de la Trap*** (return-from-trap): reduce el nivel de privilegios a user mode.

#### ¿Como sabe la trap que código ejecutar?
El kernel lo hace configurando una ***tabla de traps*** (trap table) en el momento de booteo.
Configura el hardware a su placer, el SO informa al hardware de las ubicaciones de los ***gestores de traps*** (trap handlers), usualmente con alguna instrucción y el hardware recuerda la ubicación hasta que la maquina es reiniciada (se rebootea).
Para especificar la system call, se le asigna un numero. El código de usuario es el responsable de colocar el numero deseado en un registro o en una ubicación en el stack. El SO al encargarse del system call dentro del trap handler, examina el numero, se asegura de que sea valido, y si lo es, ejecuta el código.

## Problema 2: Switching entre procesos
Si un proceso se esta ejecutando en la CPU, significa que el SO no esta corriendo. Si el SO no se esta ejecutando ¿Como puede hacer algo?, Rta: no puede.

1. ***Enfoque cooperativo*** (Esperar las llamadas al sistema): El SO confía en que el programa en algún momento el programa va a ejecutar una syscall y que así va a retornar el control de la CPU, re de iluso, si te atascas en un infinito loop toca reiniciar la compu pa.
2. ***Enfoque no cooperativo*** (El SO toma el control): A través de un timer interrupt, que cada cierto tiempo, le da el control de vuelta al SO para que el decida que hacer (empezar el timer es una operacion privilegiada).

#### ¿Como un proceso cede la CPU?
La mayoría de los procesos, transfieren el control de la CPU al SO con bastante frecuencia haciendo ***llamadas al sistema***. Los sistemas a menudo incluyen una llamada al sistema explicita ***yield()***: transfiere el control al SO para que pueda ejecutar otros procesos. Las aplicaciones también transfieren el control al SO cuando hacen algo ***ilegal***, por ejemplo: dividir por cero, o intentar acceder a memoria a la que no debería.

## Guardar y Restaurar el Contexto
Ya que el SO ha recuperado el control (ya sea de forma cooperativa a través de una llamada al sistema o de forma mas forzada a través de un interrupción por tiempo), se debe tomar una decisión si continuar corriendo el proceso que se esta ejecutando actualmente o cambiarlo. Esta decisión la toma el ***[Planificador](Cap7_Planificación_Introducción)***.
Si cambiamos de proceso, el SO ejecuta un fragmento de código de bajo nivel al que llamamos ***cambio de contexto*** (context switch): Todo lo que el SO tiene que hacer es guardar algunos valores de registros para el proceso que se esta ejecutando actualmente (por ejemplo: en su stack de kernel) y restaurar algunos para el proceso que pronto se ejecutara (desde su stack de kernel) 

## ¿Te preocupa la concurrencia?
#### ¿Que pasa cuando, durante la llamada al sistema, ocurre un interrupción del temporizador?¿Que pasa cuando manejas una interrupción y ocurre otra?¿No se vuelve difícil de manejar en el kernel?
SI, el SO debe preocuparse por lo que sucede si, durante la interrupción o el manejo de traps, se produce otra interrupción.
Una cosa simple que puede hacer el SO es deshabilitar las interrupciones durante el manejo de interrupciones; al hacerlo, se asegura que cuando se procesa una interrupción, no llegara otra a la CPU. El SO debe tener cuidado al hacerlo, deshabilitar las interrupciones por mucho tiempo podría provocar la perdida de interrupciones xd¿? (en términos técnicos es malo).
