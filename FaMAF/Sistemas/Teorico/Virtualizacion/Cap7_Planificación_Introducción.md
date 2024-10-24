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

![[tiempoDeEntrega.png]]

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
***Trabajo de Menor Tiempo Restante Primero*** (***STCF***: Shortest Time-to-Completion Firts) o ***Trabajo Mas Corto Primero con Apropiación*** (***PSJF***: Preemtive Shortest Job First). Cada vez que un nuevo trabajo ingresa al sistema, STCF determina a cual de los trabajos restantes (incluyendo al que llego) le queda menor tiempo hasta finalizar, y lo elige para ser ejecutado. Entonces en el ejemplo anterior, STCF habría detenido a Ay ejecutado a B y a C hasta su finalización; y recién cuando hayan terminado, habría elegido ejecutar lo que quede de A.



