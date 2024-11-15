## Declaración
Dado un tipo T, un ***Puntero a T*** es un tipo de datos que representa el lugar en la memoria en donde está alojado un elemento de tipo ***T***.
Por ejemplo se puede declarar un puntero a ***nat*** así:
```LenguajeDeLaMateria
var p: pointer to nat
```

## Operaciones
Mediante punteros el programador puede manipular la memoria disponible al ejecutar un programa.
- Para ***Resolver*** un nuevo bloque de memoria donde puede almacenar un elemento del tipo que apunta se utiliza la operación ***alloc***
```LenguajeDeLaMateria
alloc(p)
```
- Puedo acceder al valor en el bloque de memoria apuntado por "p" mediante la operación * .
```LenguajeDeLaMateria
*p:= 10
```
decimos que "p" ***apunta*** al valor 10.
- Para ***Liberar*** un bloque de memoria que haya sido reservado antes se utiliza la operación ***free***
```LenguajeDeLaMateroa
free(p)
```
Luego de realizar un free, "p" ya no apunta a un bloque de memoria reservado por el programa, por lo cual no se puede saber qué valor tiene la expresión `*p`.
- Existe una constante para representar punteros que no aputen a nada, a la que llamamos ***null***
```LenguajeDeLaMateria
p:= null
```

## Representación gráfica
Hay distintas representaciones gráficas, una para cada de las posibles situaciones. Dado 
p: ***pointer to*** tperson
![[representacion grafica punteros.png]]
- En la situación (1), el valor de "p" es ***null***, "p" no señala ninguna posición de memoria.
- En la situación (2) la posición de memoria señalada por "p" no está reservada, por ejemplo: inmediatamente después de `free(p)`.
- En la situación (3) el valor de "p" es la dirección de memoria donde se aloja la tperson representada gráficamente al final de la flecha, por ejemplo: inmediatamente después de `alloc(p)`.
- en la situación (3), `*p` denota la tperson que se encuentra señalada por "p", y por lo tanto, `*p.name` , `*p.age` y `*p.weight` son sus campos.
- Esta notación permite acceder a la información alojada en la tperson y modificarla mediante asignaciones a sus campos, por ejemplo: `*p.name:= "Juan"`.

## Notación
- Una notación conveniente para acceder a los campos de una tupla señalada por un puntero es la flecha "-->".
- Así, en vez de escribir `*p.name`, podemos escribir ***p-->name*** tanto para leer ese campo como para modificarlo.
- Esta notación reemplaza el uso de dos operadores ("`*`" y "`.`") por uno visualmente más apropiado, por ejemplo: p-->name:= "Juan".
- La notación `*p` y sus derivadas `*p.name`, p-->name, etc. Sólo pueden utilizarse en la situación (3).

## Puntero colgante y null
En la situación (2) el valor de "p" es inconsistente, no debe utilizarse ni accederse una dirección de memoria no reservada ya que no se sabe, a priori, qué verga hay en ella (en particular puede haber sido reservada para otro uso y al modificarlo se estaría corrompiendo información importante para tal uso). Los punteros que se encuentran en la situación (2) se llaman ***Referencias*** o ***Punteros Colgantes*** ("[Dangling pointers](https://en.wikipedia.org/wiki/Dangling_pointer#:~:text=a%20valid%20destination.-,Dangling%20pointer,location%20of%20the%20deallocated%20memory.)").
En la situación (1) el valor de "p" es ***null***, o sea que "p" no señala ninguna posición de memoria. Por ello, no tiene sentido intentar acceder a ella.

## Aliasing
Los punteros permiten manejar explícitamente direcciones de memoria. Esto no es sencillo, aparecen situaciones que con los tipos de datos usuales no se daban. Por ejemplo:
![[ejemplo.png]]

`q:= p`

![[Ejemplo parte 2.png]]

Después de la asignación, "q" y "p" señalan a la misma tupla, por lo que cualquier modificación de campos de `*q` también modifican los de `*p` (ya que son lo mismo xd) y viceversa.
Esto es lo que se llama ***Aliasing***, o sea, hay 2 nombres distintos (`*p`y `*q`) para el mismo objeto y al modificar uno se modifica el otro. 

## Administración de memoria
Hasta ahora asumimos que no es necesario ocuparse de reservar y liberar espacios de memoria para las variables. Los punteros como "p" y "q" son variables, así que ***Tampoco es necesario reservar y liberar espacios para ellos***. Pero las operaciones `alloc` y `free` son responsables de reservar y liberar explícitamente espacios ***Para los objetos que "p" y "q" señalan***.
Esta posibilidad significa ciertas libertades: el programador puede decidir exactamente cuándo reservar espacios para una tupla. Por otro lado, significa también más responsabilidades: el programador es el que debe encargarse de liberar el espacio cuando deje de ser necesario.
El verdadero beneficio de los punteros radica en que permiten una gran flexibilidad para representar diferentes tipos abstractos.