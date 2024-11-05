## Declaración
- Los arreglos son colecciones de tamaño fijo de elementos del mismo tipo.
- En general los lenguajes de programación implementan los arreglos alejándonos en espacios contiguos de memoria, y el acceso a cada uno de sus elementos se obtiene en tiempo constante.
- Para declarar un arreglo en un programa debemos indicar de que tipo ***T*** son los elementos y dos números naturales ***M*** y ***N*** indicando el primer y último índice respectivamente:
```LenguajeDeLaMateria
var a: array[M..N] of T
```
- El arreglo "a" tiene N-M elementos de tipo T.

## Índices
- Comúnmente en otros lenguajes como C, los índices siempre comienzan en 0, y entonces solo debo indicar cuántos elementos tiene el arreglo.
- Nuestro lenguaje es mas flexible, permitiendo indicar el índice del comienzo y final del arreglo, y también permitimos utilizar como índice a los elementos de un tipo enumerado:
```LenguajeDeLaMateria
var a: array[elem1...elemK] of T
```
donde elem1 ... elemK son los elementos del tipo enumerado definido previamente.
```LenguajeDeLaMateria
var page: array['a' ... 'z'] of Nat
```

- También es frecuente la utilización de arreglos multidimensionales,
- Ejemplo:
```LenguajeDeLaMateria
var b: array[1 ... N, 1 ... M] of String
var c: array[1 ... N, 'a' ... 'z', 1 ... M] of Nat
```
- para acceder a los elementos de un arreglo utilizamos los corchetes, separando con comas cuando tenemos más de una dimensión:
```LenguajeDeLaMateria
b[2, 3]:= "casa"
c[2, 'x', 1] = 25
```

## Ejemplo de inicialización
- Si quiero inicializar el arreglo "c" puedo utilizar tres ***for*** anidados, de la siguiente forma:
```LenguajeDeLaMateria
for i:= M to N do
	for k:= 'a' to 'z' do
		for d:= 1 to M do
			c[i, k, d]:= 0
		od
	od
od
```