- Tenemos una mochila de capacidad W.
- Tenemos ' n ' objetos de valor $V_1$, $V_2$, ..., $V_n$ y peso $W_1$, $W_2$, ..., $W_n$
- Se quiere encontrar la mejor selección de objetos para llevar en la mochila.
- Por mejor selección se entiende aquélla que totaliza el ***Mayor valor posible*** sin que su peso exceda la capacidad W de la mochila.
- Para que el problema no sea trivial, asumimos que la suma de los pesos de los ' n ' objetos excede la capacidad de la mochila, obligándonos entonces a seleccionar cuáles cargar en ella.
---
### Criterio de selección 
¿Cómo conviene seleccionar un objeto para cargar en la mochila?
1. El más valioso de todos.
2. El menos pesado de todos.
3. Una combinación de los dos.
---
### Análisis del primer criterio de selección
El más valioso primero
- ***Razonabilidad***: El objetivo es cargar la mochila con el mayor valor posible, escogemos los objetos más valiosos.
- ***Falla***: Puede que al elegir un objeto valioso dejemos de lado otro apenas menos valioso pero mas liviano.
- Ejemplo: Mochila de capacidad 10, objetos de valor 12, 11 y 9, y peso de 7, 5 y 5.
- De elegir primero el de mayor valor (12) ocuparíamos 7 de los 10kg de la mochila, no quedando lugar para otro objeto.
- En cambio, de elegir el de valor 11, ocuparíamos solamente 5kg quedando 5kg para el de valor 9, totalizando un valor de 20.
---
### Análisis del segundo criterio de selección
El menos pesado primero
- ***Razonabilidad***: hay que procurar aprovechar la capacidad de la mochila, escogemos los objetos de más livianos.
- ***Falla***: Puede que al elegir un objeto liviano dejemos de lado otro apenas más pesado pero mucho más pesado.
- Ejemplo: Mochila de capacidad 13, objetos de valor 12, 11 y 7, y peso 6, 6 y 5.
- De elegir primero el de menor peso (5) obtendríamos su valor (7) más, en el mejor de los casos, 12, totalizando 12 + 7 = 19.
- En cambio, de elegir los dos de peso 6, no se excede la capacidad de la mochila y se totaliza un valor de 23.
---
### Análisis del tercer criterio de selección
Combinando ambos criterios
- Debemos asegurarnos de que cada kg utilizado de la mochila sea aprovechado de la mejor manera posible: que cada kg colocado en la mochila valga lo más posible.
- ***Criterio***: Elegir el de mayor valor relativo (cociente entre el valor y el peso): Dicho cociente expresa el valor promedio de cada kg de ese objeto.
- ***Falla***: Puede que al elegir un objeto dejemos de lado otro peor cociente, pero que aprovecha mejor la capacidad.
- Ejemplo: Mochila de capacidad 10, objetos de valor 12, 11 y 8, y peso 6, 5 y 4.
- El criterio elige al que pesa 5, ya que cada kg de ese objeto vale más de 2. Pero convenía elegir los otros dos.
--- 
### Versión simplificada del Problema de la Mochila
- El problema de la mochila no admite solución voraz.
- Se simplifica permitiendo ***Fraccionar*** objetos.
- Ahora sí el tercer criterio funciona.
- (En el ejemplo anterior, eligiendo primero el que vale 11 y luego 5/6 del que vale 12 obteniendo como valor total 11 + 10 = 21).

Ya tenemos definido el criterio de selección. Antes de implementar el algoritmo definimos un tipo de dato para representar los objetos de la mochila.

---
### Estructura de Datos necesario
```LenguajeDeLaMateria
type Objeto = tuple
				id: Nat
				value: Float
				weight: Float
			  end tuple
```
Resolveremos el problema asumiendo que los datos vienen dados por un conjunto C: Set of Objeto.
La solución la podemos representar por una lista donde cada elemento es un par conteniendo el objeto y la fracción que agrego de él:
```LenguajeDeLaMateria
type Obj_Mochila = tuple
						obj: Objeto
						fract: Float
				   end tuple
```
---
### Algoritmo Voraz
```LenguajeDeLaMateria
fun mochila(W: Float, C: Set of Objeto) ret L: List of Obj_Mochila
	var o_m: Obj_Mochila
	var resto: Float
	var C_aux: Set of Objeto
	S:= empty_list()
	C_aux:= set_copy(C)
	resto:= W
	do (resto > 0) --->
		o_m.obj:= select_obj(C_aux)
		if (o_m.obj.weight <= resto) then
			o_m.fract:= 1.0
			resto:= resto - 0_m.obj.weight
		else
			o_m.fract:= resto / o_m.obj.weight
			resto:= 0
		fi
		addl(S, o_m)
		elim(C_aux, o_m.obj)
	od
	set_destroy(C_aux)
end fun

fun select_obj(C: Set of Objeto) ret r: Objeto
	var C_aux: Set of Objeto
	var o: Objeto
	var m: Float
	m:= -∞
	C_aux:= set_copy(C)
	do (not is_empty_set(C_aux)) --->
		o:= get(C_aux)
		if (o.value / o.weight > m) then
			m:= o.value / o.weight
			r:= o
		fi
		elim(C_aux, o)
	od
	set_destroy(C_aux)
end fun
```
---
### Sobre este Algoritmo
- Funciona siempre que esté permitido fraccionar objetos.
- La función `select_obj` implementa el criterio de selección, eligiendo del conjunto de objetos, aquel que tiene la relación valor/peso máxima.
- La solución al problema puede implementarse también utilizando arreglos.