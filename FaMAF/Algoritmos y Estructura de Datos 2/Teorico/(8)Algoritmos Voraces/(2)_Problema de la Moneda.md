- Tenemos una cantidad infinita de monedas de cada una de las siguientes denominaciones:
	- 1 Peso,
	- 50 Centavos,
	- 25 Centavos,
	- 10 Centavos,
	- 5 Centavos,
	- y 1 Centavo.
- Se desea pagar un cierto monto de manera exacta.
- Se debe determinar la manera de pagar dicho importe exacto con la ***Menor cantidad de monedas posibles***.
--- 
### Solución al problema
- Seleccionar una moneda de la mayor denominación posible que no exceda el monto a pagar,
- utilizar exactamente el mismo algoritmo para el importe remanente. 

Ya tenemos definido el ***Criterio de Selección***.

Antes de implementar la solución, debemos pensar con qué ***Estructura de Datos*** vamos a representar los elementos que intervienen en el problema. En este caso utilizamos naturales para cada una de las denominación de moneda, un natural para el monto a pagar, y un natural para la solución que indica la cantidad de monedas necesarias para pagar.

---
### Algoritmo voraz: versión 1
```LenguajeDeLaMateria
fun cambio(m: Nat, C: Set of Nat) ret S: Nat
	var c, resto: Nat
	var C_aux: Set of Nat
	S:= 0
	C_aux:= set_copy(C)
	resto:= m
	do (resto > 0) --->
		c:= "Elemento máximo de C_aux"
		elim(C_aux, c)
		S:= S + resto 'div' c
		resto:= resto 'mod' c
	od
	set_destroy(C_aux)
end fun
```
---
### Algoritmo voraz: Versión 2
```LenguajeDeLaMateria
fun cambio(m: Nat, C: Set of Nat) ret S: Nat
	var c, resto: Nat
	var C_aux: Set of Nat
	S:= 0
	C_aux:= set_copy(C)
	resto:= m
	do (not is_empty_set(C_aux)) --->
		c:= "Elemento máximo de C_aux" {- Selecciono -}
		if (c > resto) then {- Chequeo factibilidad -}
			elim(C_aux, c)
		else
			resto:= resto - c
			S:= S + 1 {- Agrego a la solución -}
		fi
	od
	set_destroy(C_aux)
end fun
```
---
### Algoritmo voraz: Devolviendo el valor de cada moneda
```LenguajeDeLaMateria
fun cambio(m: Nat, C: Set of Nat) ret S: List of Nat
	var c, resto: Nat
	var C_aux: Set of Nat
	S:= empty_list()
	C_aux:= set_copy(C)
	resto:= m
	do (not is_empty_set(C_aux)) --->
		c:= "Elemento máximo de C_aux" {- Selecciono -}
		if (c > resto) then {- Chequeo factibilidad -}
			elim(C_aux, c)
		else
			resto:= resto - c
			addl(S, c) {- Agrego a la solución -}
		fi
	od
	set_destroy(C_aux)
end fun
```
---
### Sobre el Algoritmo
- La ***Versión 1*** tiene el problema de que para algunos conjuntos de denominaciones puede que el ciclo no termine nunca o bien que la ejecución falle al querer obtener el máximo de un conjunto vacío.
- En ambos casos resta por implementar la función que elige el máximo de un conjunto.
- El algoritmo no siempre obtiene la solución óptima, depende del conjunto de denominaciones.
- Para un conjunto razonable, funciona.
- Para denominaciones habituales: 100, 50, 25, 10, 5 y 1 funciona
---
### Conjunto de Denominaciones para el que NO Funciona
- Sean 4, 3 y 1 las denominaciones y sea 6 el monto a pagar.
- El algoritmo voraz intenta pagar con una moneda de denominación 4, queda un saldo de 2 que solamente puede pagarse con 2 monedas de 1, en total, 3 monedas.
- Pero hay una solución mejor: dos monedas de 3.
- De todas formas, el algoritmo anda bien para todas las denominaciones de uso habitual.
