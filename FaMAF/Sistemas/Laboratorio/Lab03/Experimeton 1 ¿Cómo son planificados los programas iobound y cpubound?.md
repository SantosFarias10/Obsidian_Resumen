
En esta sección van a tener que completar los programas [[iobench]] y [[cpubench]] con una ***métrica*** que les permita comparar la cantidad de operaciones de cada tipo que se realizan a medida que cambiemos los parámetros del sistema operativo.

En cada ciclo de medición las operaciones (de I/O o de cómputo) se ejecutarán muchas veces. El largo del experimento (o sea, cuántas veces se ejecutan las operaciones) y el tamaño de las operaciones pueden cambiarlo en base a los siguientes criterios:

1. Que el experimento sea lo suficientemente largo como para que haya cambios de contexto entre los procesos.
2. Que el experimento sea lo suficientemente corto como para que el programa entero de mediciones se ejecute en un tiempo razonable, digamos 1 minuto.
3. Que compile: si las operaciones son demasiado grandes, podemos quedarnos sin memoria en el al ejecutar muchos procesos iobench o que el conteo de la cantidad de operaciones de cpu de overflow. 
4. Que las mediciones no sean menores que ni cercanas a 1 en la mayoría de los casos, ya que son casteadas a enteros y se pierde mucha información en el redondeo.

Utilizando el largo de quantum 10 veces más pequeño que el original y un valor de N constante, medir cuántas veces son planificados los procesos en los siguientes escenarios:

1. iobench N &
    
2. iobench N &; iobench N &; iobench N &
    
3. cpubench N &
    
4. cpubench N &; cpubench N &; cpubench N &
    
5. iobench N &; cpubench N &; cpubench N &; cpubench N &
    
6. cpubench N &; iobench N &; iobench N &; iobench N &

Otra forma de hacer estos experimentos es utilizando sólo la salida de uno de ellos. En este caso medimos la performance de un proceso en particular dado los otros procesos del entorno. Eso se puede realizar con un comando como  

iobench 3 | iobench 3 | iobench 3 &

cpubench 3 | cpubench 3 | cpubench 3 &

Responder las siguientes preguntas utilizando gráficos y/o tablas para justificar sus respuestas:

1. Describa los parámetros de los programas cpubench e iobench para este experimento (o sea, los define al principio y el valor de N. Tener en cuenta que podrían cambiar en experimentos futuros, pero que si lo hacen los resultados ya no serán comparables).
    
2. ¿Los procesos se ejecutan en paralelo? ¿En promedio, qué proceso o procesos se ejecutan primero? Hacer una observación cualitativa.
    
3. ¿Cambia el rendimiento de los procesos iobound con respecto a la cantidad y tipo de procesos que se estén ejecutando en paralelo? ¿Por qué?
    
4. ¿Cambia el rendimiento de los procesos cpubound con respecto a la cantidad y tipo de procesos que se estén ejecutando en paralelo? ¿Por qué?
    
5. ¿Es adecuado comparar la cantidad de operaciones de cpu con la cantidad de operaciones iobound?