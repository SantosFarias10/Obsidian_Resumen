## Suposiciones Sobre la Carga de Trabajo
Planteamos algunas suposiciones para simplificar los procesos que se ejecutan en el sistema, denominados como ***Carga de Trabajo*** (workload).
Las suposiciones que vamos a plantear son (la mayoría) pocos realistas, por lo que a medida que avancemos las vamos a flexibilizar.
Suposiciones:
1. Cada trabajo se ejecuta durante la misma cantidad de tiempo.
2. Todos los trabajos llegan al mismo tiempo.
3. Una vez iniciado, cada trabajo se ejecuta hasta su finalización.
4. Todos los trabajos usan la CPU (o sea, no realizan I/O).
5. Se conoce el tiempo de ejecución de cada trabajo.

## Métrica de Planificación
Ademas de las suposiciones necesitamos algo que nos permita comparar diferentes políticas de planificación: ***Métrica de Planificación***.
Por ahora vamos a tener en cuenta una única ***Métrica***: el ***Tiempo de entrega***: se define como el momento en que se completa el trabajo menos el momento en que el trabajo llego al sistema. 

$T_{\text{entrega}} = T_{\text{finalizacion}} - T_{\text{llegada}}$

Como asumimos, por suposición ***2***, entonces por el momento diremos que $T_{\text{llegada}} = 0$, por lo tanto $T_{\text{entrega}} = T_{\text{finalizacion}}$. Esto irá cambiando a medida que saquemos las suposiciones.

## Primero En Entrar, Primero En Salir (FIFO)
***Primero en Entrar, Primero en Salir*** (***FIFO***: First In, First Out) o también como ***Primero en Llegar, Primero en Ser Atendido*** (***FCFS***: First Come, First Served). Es un algoritmo muy básico, es fácil de implementar, dadas nuestras suposiciones funciona bien 👍🏾.
Ejemplo: Llegan 3 trabajos al sistema, A, B y C, aproximadamente al mismo tiempo 
($T_{\text{llegada}} = 0$). como FIFO tiene que poner un trabajo primero entonces vamos a suponer que A llego justo antes que B, que a su vez llego un ratito antes que C. Supongamos que todos se ejecutan durante 10 segundos. ¿Cual es el ***tiempo medio de entrega*** de los trabajos?

![[Figure 7.1: Ejemplo Simple de FIFO.png]]
Figure 7.1: Ejemplo para petes de FIFO

Vemos que A termino en 10, B en 20 y C en 30, entonces el ***tiempo medio de entrega*** para los tres trabajos es $\frac{10 + 20 + 30}{3} = 20$.
Ahora olvidemos una de las suposiciones, puntualmente saquemos la suposición ***1*** (Todos los trabajos se ejecutan durante la misma cantidad de tiempo).

#### ¿Como funciona FIFO ahora?¿Que tipo de carga de trabajo podrías construir que FIFO ahora?
Si pensas un poquito la sacas.
Ejemplo: Supongamos de nuevo tres trabajos (A, B, C), pero ahora A se ejecuta durante 100 segundos antes de que B o C tengan la oportunidad de ejecutarse. Por lo que el tiempo de medio de entrega  del sistema es bastante algo ($\frac{100 + 110 + 120}{3} = 110$).

![[Figure 7.2: Por Qu  ́e FIFO No Es Tan Genial.png]]
Figure 7.2: Porque FIFO no es cool.

Este problema se conoce como ***Efecto de Convoy***: una serie de consumidores potenciales de algún recurso, relativamente cortos, se ponen en cola detrás de un consumidor de gran peso.

## Trabajos Más Cortos Primero (SJF)
Resulta que hay un enfoque que resuelve este problema, ***Trabajo Mas Corto Primero*** (***SJF***: Shortest Job First), primero ejecuta el trabajo mas corto, luego el siguiente y así sucesivamente.
Tomemos el ejemplo anterior, pero con SJF como nuestra política de planificación.

![[Figure 7.3: Ejemplo Simple de SJF.png]]
Figure 7.3: Ejemplo para petes de SJF

El diagrama debería aclarar porque SJF tiene un rendimiento mucho mejor con respecto al tiempo medio de entrega. Simplemente ejecutar antes B y C, SJF reduce el tiempo medio de entrega de 110 segundos a 50 ($\frac{10 + 20 + 120}{3} = 50$)
Ahora olvidemos de la suposición ***2*** (Todos los trabajos llegan al mismo tiempo), entonces asumimos que los trabajos pueden llegar en cualquier momento.

#### ¿A que problema conduce esto?
Ejemplo: Supongamos que A llega en $T = 0$ y necesita ejecutarse durante 100 segundos, mientras que B y C llegan en $T = 10$ y cada uno se ejecuta durante 10 segundos.

![[Figure 7.4: SJF Con Llegadas Tard ́ıas de B y C.png]]
Figure 7.4: SJF Con Llegadas Tardías de B y C.

Aunque B y C llegaron poco después de A, todavía se ven obligados a esperar a que A termine y sufren el mismo problema convoy. El tiempo medio de entrega para estos tres trabajos es $\frac{100 + (110 - 10) + (120 - 10)}{3} = 103,33$ segundos. (ver que el 100 es porque A llega en $T = 0$ y su tiempo de ejecución es de 100 => $100-0 = 100$, el (110 - 10) es porque B termina de ejecutarse en $T = 110$ y su tiempo de ejecución es de 10 => por la formula el tiempo de llegada es $T_{\text{entrega}} = T_{\text{finalizacion}} - T_{\text{llegada}}$ => 110 - 10, igual con el C xd)

## Trabajo de Menor Tiempo Restante Primero (STCF)
Olvidamos el supuesto ***3*** (los trabajos deben ejecutarse hasta su finalizacion).
***Trabajo de Menor Tiempo Restante Primero*** (***STCF***: Shortest Time-to-Completion Firts) o ***Trabajo Mas Corto Primero con Apropiación*** (***PSJF***: Preemtive Shortest Job First). Cada vez que un nuevo trabajo ingresa al sistema, STCF determina a cual de los trabajos restantes (incluyendo al que llego) le queda menor tiempo hasta finalizar, y lo elige para ser ejecutado. Entonces en el ejemplo anterior, STCF habría detenido a A y ejecutado a B y a C hasta su finalización; y recién cuando hayan terminado, habría elegido ejecutar lo que quede de A.

![[Figure 7.5: Ejemplo simple de STCF.png]]
Figure 7.5: Ejemplo para petes de STCF

El resultado es un tiempo medio de entrega mucho mejor: $\frac{((120 - 0) + (20 - 10) + (30 - 10))}{3} = 50$ segundos.

## Una Nueva Métrica: El Tiempo de Respuesta
***Tiempo de Respuesta***: Es el tiempo desde que el trabajo llega a un sistema hasta la primera vez que es elegido para ser ejecutado:

$T_{\text{respuesta}} = T_{\text{1era - ejecucion}} - T_{\text{llegada}}$

Ejemplo: si tenemos el programa de la figura 7.5 (con ***A*** llegando en $T = 0$, y ***B*** y ***C*** en $T = 10$), el tiempo de respuesta de cada trabajo seria: 0 para el trabajo A, 0 para B y 10 para C (una media de 3,33) (ver que en C llega en $T = 0$ y su primera ejecución es en $T = 10$ => 20 - 10 = 10).
STCF no es bueno con el tiempo de respuesta. Si tres trabajos llegan al mismo tiempo, el tercer trabajo tiene que esperar a que los dos trabajos anteriores se ejecuten en su totalidad antes de ser elegido por primera vez.

## Round Robin
Round Robin (RR): en lugar de ejecutar un trabajo hasta su finalización, RR ejecuta cada trabajo durante un ***Segmento de Tiempo*** (llamado ***Quantum de Planificación***) y luego cambia el siguiente trabajo en la cola de ejecución. Esto lo hace repetidamente hasta que se terminan todos los trabajos. Notar que la duración de un segmento de tiempo debe un múltiplo del periodo de interrupción del temporizador. Por ejemplo: si el temporizador se interrumpe cada 10ms, el segmento de tiempo podría ser de 10, 20 o cualquier múltiplo de 10ms.
Ejemplo: Supongamos tres trabajos A, B y C llegan al mismo tiempo al sistema y cada uno desea ejecutarse durante 5 segundos. Un planificador SJF ejecutaría cada trabajo hasta su finalización antes de ejecutar otro

![[Figure 7.6: SJF de Nuevo (Malo Para el Tiempo de Respuesta).png]]
Figure 7.6: Otra vez SJF (un pete en el tiempo de respuesta)

Por lo contrario, RR con un segmento de tiempo de 1 segundo recorrería los trabajos rápidamente.

![[Figure 7.7: Round Robin (Bueno Para el Tiempo de Respuesta).png]]
Figure 7.7: RR (Godines para el tiempo de respuesta)

El tiempo promedio de ***Respuesta*** de RR es de $\frac{0 + 1 + 2}{3} = 1$; Para el SJF, el tiempo medio de ***Respuesta*** es de $\frac{0 + 5 + 10}{3} = 5$.
Cuanto mas corto sea el segmento de tiempo, mejor sera el rendimiento de RR según la métrica del tiempo de respuesta, pero si el segmento de tiempo es demasiado corto puede resultar problemático, ya que el cambio de contexto dominaría el rendimiento general.

#### ¿Que pasa con el tiempo de entrega?
Ejemplo: A, B y C, cada uno con tiempos de ejecución de 5 segundos, llegan al mismo tiempo, y el planificador es de tipo RR con un (largo) segmento de tiempo de 1 segundo. Por la figura 7.7 vemos que A termina en 13, B en 14, y C en 15, para una media de 14 (horrible).
RR es una de las peores políticas si nuestra métrica es el tiempo de entrega, ya que RR estira cada trabajo tanto como pueda, ejecutando cada trabajo por muy poco tiempo antes de pasar al siguiente.

## Incorporando I/O
Ahora olvidemos el supuesto 4 [Todos los trabajos usan la CPU (o sea, no realizan I/O)]: obviamente todos los programas realizan I/O.
El planificador tiene que tomar una decisión cuando algún trabajo inicia una solicitud de I/O, ya que el trabajo que se esta ejecutando deja de usar la CPU durante la I/O, y se queda ***Bloqueado*** esperando su finalización.
El Planificador también debe tomar una decisión cuando se termina la I/O. Cuando esto ocurre, se genera una interrupción y se ejecuta el SO, moviendo el proceso que emitió la I/O del estado bloqueado al estado listo.
Supongamos que tenemos dos trabajos, A y B, que necesitan cada uno 50ms de tiempo de CPU. Pero, hay una diferencia: A se ejecuta durante 10ms y después emite una solicitud de I/O (supongamos que las  I/O tardan 10ms cada una), mientras que B simplemente usa la CPU durante 50ms y no realiza ninguna I/O. El planificado ejecuta primero a A, y a B después.

![[Figure 7.8: Mal Uso de los Recursos.png]]
Figure 7.8: Mal Uso de los Recursos.

Un enfoque común es tratar cada subtrabajo de 10ms de A como un trabajo independiente. Por lo que, cuando el sistema se inicia, su elección esta entre ejecutar un A de 10ms o un B de 50ms. Con STCF (Trabajo de Menor Tiempo Restante Primero): elige el mas corto, en este caso A. Después, cuando el primer subtrabajo termina, solo queda B y comienza su ejecución. Luego se envía un nuevo subtrabajo de A, que se adelanta a B y se ejecuta durante 10ms. De esta manera se permite la ***Superposición***, con la CPU siendo utilizando por un procesos mientras espera que se complete la I/O de otro proceso; por lo tanto, el sistema es mejor utilizado

![[Figure 7.9: Superponer Permite un Mejor Uso de los Recursos.png]]
Figure 7.9: Superponer Permite un Mejor Uso de los Recursos.

## No Más Oráculo
Llegamos a la ultima suposición (el planificador conoce la duración de cada trabajo). El SO generalmente sabe muy poco sobre la duración de cada trabajo. Entonces ¿Como podemos construir un enfoque que se comporte como SJF/STCF sin ese conocimiento a priori? ¿Como podemos incorporar algunas de las ideas que hemos visto con el planificador RR para que el tiempo de respuesta también sea bastante bueno? La respuesta es MLFQ que la vamos en el cap8 xd.