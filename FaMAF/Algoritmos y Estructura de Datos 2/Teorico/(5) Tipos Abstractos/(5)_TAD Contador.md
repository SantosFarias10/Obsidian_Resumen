## Paréntesis balanceados
- Problemas
	- Dar un algoritmo que tome una expresión,
	- dada, por ejemplo, por un arreglo de caracteres, 
	- y devuelva verdadero si la expresión tiene sus paréntesis correctamente balanceados,
	- y falso en caso contrario.

## Solución conocida
- Recorrer el arreglo de izquierda a derecha,
- utilizando un entero ***Inicializado en 0***,
- ***Incrementarlo*** cada vez que se encuentre un paréntesis que abre,
- ***Decrementarlo*** (comprobando previamente que no sea nulo en cuyo caso ***no están balanceados***) cada vez que se encuentran un paréntesis que cierra.
- Al finalizar, ***Comprobar*** que dicho entero sea cero.
---
## Contador
- No hace falta un entero (susceptible de numerosas operaciones aritméticas),
- sólo se necesita ***Algo*** con lo que se pueda
	- inicializar
	- incrementar
	- comprobar si su valor es el inicial
	- decrementar si no lo es
- Llamaremos a ese ***Algo, contador***
- Notar que las operaciones ***Inicializar*** e ***Incrementar*** son capaces de generar todos los valores posibles del contador, por lo que serán nuestros ***Constructores***.
- ***Comprobar*** en cambio solamente examina el contador,
- ***Decrementar*** no genera más valores que los obtenibles por ***Inicializar*** e ***Incrementar***
---
## Especificar del TAD Contador
```LenguajeDeLaMateria
spec Counter where
```

***Constructors***
```LenguajeDeLaMateria
fun init() ret c: Counter
{- crea un contador inicial. -}

proc incr(in/out c: Counter)
{- incrementar el contador c. -}
```

***Destroy***
```LenguajeDeLaMateria
proc destroy(in/out c: Counter)
{- Libera memoria en caso que sea necesario. -}
```

***Operations***
```LenguajeDeLaMateria
fun is_init(c: Counter) ret b: bool
{- Devuelve True si el contador es inicial -}

proc decr(in/out c: Counter)
{- Decrementa el contador c. -}
{- PRE: not is_init(c) -}
```
---
## Resolviendo el problema
- Queremos implementar un algoritmo que resuelva el problema de los paréntesis balanceados utilizando el TAD contador.
- La especificación nos da toda la información que necesitamos tener: constructores y operaciones con sus tipos.
- La idea es iniciar un contador y recorrer el arreglo de caracteres de izquierda a derecha.
- Si encontramos un paréntesis que abre, incrementamos el contador.
- Si encontramos un paréntesis que cierra, lo decrementamos.
- Si el contador es inicial y encuentro paréntesis que cierra devuelvo False. Si termino de recorrer el arreglo y el contador no es inicial también doy False.

## Algoritmo de control de paréntesis balanceados
```LenguajeDeLaMateria
fun matching_parenthesis(a: array[1..n] of char) ret b: bool
	var i: nat
	var c: Counter
	b:= true
	init(c)
	i:= 1
	do (1 ≤ n ∧ b) --->
		if  (a[i] = '(' ) ---> inc(c)
			(a[i] = ')' ∧ is_init(c)) ---> b:= false
			(a[i] = ’)’ ∧ ¬is_init(c)) ---> dec(c)
		fi
		i:= i + 1
	od
	b:= b ∧ is_init(c)
	destroy(c)
end fun
```

## Implementación del TAD Contador
```LenguajeDeLaMateria
implement counter where

type Counter = nat

proc init(out c: Counter)
	c:= 0
end proc

proc inc(in/out c: Counter)
	c:= c + 1
end proc

fun is_empty(c: Counter) ret b: bool
	b:= (c = 0)
end fun

{- PRE: not is_init(c) -}
proc dec(in/out c: Counter)
	c:= c - 1
end proc

proc destroy(in/out c: Counter)
	skip
end proc
```
Todas las operaciones son O(1)( o grande)