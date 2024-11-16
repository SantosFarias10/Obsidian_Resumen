### Generalización de Paréntesis Balanceados
- Problema:
	- Dar un algoritmo que tome una expresión,
	- dada, por ejemplo, por un arreglo de caracteres,
	- y devuelva verdadero si la expresión tiene sus paréntesis, corchetes, llaves, etc. correctamente balanceados,
	- y falso en caso contrario.
---
#### Usando contadores
- ¿Alcanza con un contador?
	- "(1 + 2)"
	- "{1 + (18 - [4 * 2])}"
	- "(1 + 2}"
- ¿Alcanza con 3 (o "n") contadores?
	- "(1 + 2}"
	- "(1 + [3 - 1) + 4]"
---
### Conclusión
- No alcanza con saber cuántos delimitadores restan cerrar,
- también hay que saber en qué orden deben cerrarse,
- o lo que es igual
- en qué orden se han abierto,
- mejor dicho,
- ¿Cuál fue el último que se abrió? (de los que aún no se han cerrado)
- ¿y antes de ése?
- etc.
- Hace falta una "constancia" de cuáles son los delimitadores que quedan abiertos, y en qué orden deben cerrarse.
---
### Posible Solución
- Recorrer el arreglo de izquierda a derecha,
- utilizando dicha "constancia" de delimitadores aún abiertos ***Inicialmente vacía***,
- ***Agregarle*** obligación de cerrar un paréntesis (resp. corchetes, llave) cada vez que se encuentra un paréntesis (resp. corchete, llave) que abre,
- ***Removerle*** obligación de cerrar un paréntesis (resp. corchetes, llave) (***Comprobando*** previamente que la constancia no sea vacía y que la ***Primera*** obligación a cumplir sea justamente la de cerrar el paréntesis (resp. corchetes, llave)) cada vez que se encuentra un paréntesis (resp. corchetes, llave) que cierra.
- Al finaliza, ***Comprobar*** que la constancia está vacía.
---
### Pila
- Hace falta ***Algo***, una "constancia", con lo que se pueda
	- inicializar vacía,
	- agregar una obligación de cerrar delimitador,
	- comprobar si quedan obligaciones,
	- examinar la primera obligación,
	- quitar una obligación.
- La última obligación que se agregó, es la primera que debe cumplirse y quitarse de la constancia.
- Esto se llama ***Pila***.
---
### TAD Pila
- La pila se define por lo que sabemos: sus cinco operaciones:
	1) Inicializar en vacía.
	2) Apilar una nueva obligación (o elemento).
	3) Comprobar si está vacía.
	4) Examinar la primera obligación (si no está vacía).
	5) quitarla (si no está vacía).
- Nuevamente las operaciones ***Inicializar*** y ***Agregar*** son capaces de generar todas las pilas posibles,
- ***Comprobar*** y ***Examinar***, en cambio, solamente examina la pila,
- ***Quitarla*** no genera más valores que los obtenibles por ***Inicializar*** y ***Agregar***.
---
### Especificación del TAD Pila
```LenguajeDeLaMateria
spec Stack of T where
```

***Constructors***
```LenguajeDeLaMateria
fun empty_stack() ret s: Stack of T
{- Crea una pila vacía -}

proc push (in e: T, in/out s: Stack of T)
{- Agrega el elemento 'e' al tope de la pila 's' -}
```

***Operations***
```LenguajeDeLaMateria
fun is_empty_stack(s: Stack of T) ret b: bool
{- Devuelve True su la pila es vacía -}

fun top(s: Stack of T) ret e: T
{- Devuelve el elemento que se encuentra en el tope 's' -}
{- PRE: not is_empty_stack(s) -}

proc pop(in/out s: Stack of T)
{- Elimina el elemento que se encuentra en el tope de 's' -}
{- PRE: not is_empty_stack(s) -}
```
De acá para adelante omito escribir las operaciones de destrucción y copia que incluíamos en todos los TAD, pero asuman que están especificadas.

---
### Algoritmo de control de delimitadores balanceados
```LenguajeDeLaMateria
fun matching_delimiters(a: array[1..n] of char) ret b: bool
	var i: nat
	var p: stack of char
	b:= true
	p:= empty_stack()
	i:= 1
	do (i ≤ n ∧ b) --->
		if (left(a[i])) then
			  push(match(a[i], p))
		   (right(a[i]) ∧ (is_empty(p) ∨ top(p) ≠ a[i])) then
			  b:= false
		   (right(a[i]) ∧ ¬is_empty(p) ∧ top(p) = a[i]) then
			  pop(p)
		   otherwise
			  skip
		fi
		i:= i + 1
	od
	b:= b ∧ is_empty(p)
	destroy(p)
end fun
```
Este algoritmo asume, además de la implementación de pila,
- Una función ***Match*** tal que match( '(' ) = ')', match( '[' ) = ']', match( '{' ) = '}', etc.
- Una función ***left***, tal que left(’ (’ ), left( ’[’ ), left( ’{’ ), etc son verdadero, en los restantes casos left devuelve falso.
- Una función ***right***, tal que right( ’) ’), right( ’]’ ), right( ’}’ ), etc. son verdaderos, en los restantes casos, right devuelve falso.