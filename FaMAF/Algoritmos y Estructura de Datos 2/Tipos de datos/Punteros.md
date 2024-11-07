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
Luego de realizar un free, "p" ya no apunta a un bloque de memoria reservado por el programa, por lo cual no se puede saber qué valor tiene la expresión * p.
- Existe una constante para representar punteros que no aputen a nada, a la que llamamos ***null***
```LenguajeDeLaMateria
p:= null
```

## Representación gráfica
Hay distintas representaciones gráficas, una para cada de las posibles situaciones. Dado 
p: ***pointer to*** tperson
![[representacion grafica punteros.png]]
- En la situación (1), el valor de "p" es ***null***, "p" no señala ninguna posición de memoria.
- En la situación (2) la posición de memoria señalada por "p" no está reservada, por ejemplo: inmediatamente después de free(p).
- 