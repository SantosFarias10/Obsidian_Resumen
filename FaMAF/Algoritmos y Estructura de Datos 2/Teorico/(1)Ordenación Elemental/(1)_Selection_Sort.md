Selection_sort = Ordenación por selección
### Idea
- Es el algoritmo de ordenación mas sencillo (pero no el mas rápido)
- **Selecciona** el menor de todos, lo intercambia con el elemento que se encuentra en la primera posición 
- **_Selecciona_** el menor de todos **_los restantes_**, lo intercambia con el que se encuentra en el segundo lugar
- **_Selecciona_** el menor de todos **_los restantes**_, lo intercambia con el que se encuentra en tercer lugar 
- ... (en cada uno de estos pasos ordena un elemento) ... 
- hasta terminar
---
#### Ejemplo
![[EjemploSelection_sort.png]]

### Invariante:
![[InvarianteSelection_sort.png]]
- El Arreglo "a" es una permutación del original,
- Un segmento inicial a[1,i) del arreglo esta ordenado,
- dicho segmento contiene los elementos mínimos del arreglo.
---
### Pseudocódigo
*{Pre: $n≥0 ∧ a = A$ }*
``` LenguajeDeLaMateria
Proc Selection_sort (in/out a: array [1..n] of T)
	var i, minp: nat
	i:= 1          {Inv: Invariante de recién}
	do (i < n) --->
		minp:= min_pos_from(a, i)
		swap(a, i, minp)
		i:= i+1
	od
end proc
```
*{Post: 'a' está ordenado y es permutación de A}*

Con el Comando For
```LenguajeDeLaMateria
Proc Selection_sort(in/out a: array [1..n] of T)
	var minp: nat
	for i:= 1 to n-1 do
		minp:= min_pos_from(a, i)
		swap(a, i, minp)
	od
end proc
```
---
### Swap
*{Pre: $a=A ∧ 1≤i,j≤n$}*
```LenguajeDeLaMateria
Proc swap (in/out a: array[1..n] of T, in i,j: nat)
	var tmp: T
	tmp := a[i]
	a[i] := a[j]
	a[j] := tmp
end proc
```
*{Post: a[i] = A[j] ∧ a[j] = A[i] ∧ ∀k ∉ {i,j} => a[k] = A[k]}*
***¡Garantiza Permutación!***

---
### Invariante de la función de selección 
![[Inariante de la funcion de seleccion.png]]
- Invariante anterior, y
- El mínimo del segmento a[i,j] está en la posición minp
---
### min_pos_from
*{Pre: $0<i≤n$}*
```LenguajeDeLaMateria
Fun min_pos_from(a: array[1..n] of T, i: nat) ret minp: nat
	var j: nat
	minp:= i
	j:= i+1          {Inv: a[minp] es el mínimo de a[i,j)}
	do (j ≤ n) --->
		if (a[j] < a[minp]) then
			minp:= j
		fi
		j:= j+1
	od
end proc
```
*{Post: a[minp] es el mínimo de a[i,n]}*

Con el Comando For
```LenguajeDeLaMateria
Fun min_pos_from(a: array[1..n] of T, i: nat) ret minp: nat
	minp:= i
	for j:= i + 1 to n do 
		if (a[j] < a[minp]) then
			minp:= j
		fi
	od
end fun
```
---
### Comando For
Fragmentos de la siguiente forma aparecen con frecuencia:
```LenguajeDeLaMateria
k:= n
do (k ≤ m) --->
	k:= k+1
od
```

por simplicidad, lo reemplazamos por

```
for k:= n to m do C od
```

siempre que **k** no se modifique en **C**.
Ademas, asumiremos que el **for** declara la variable k, cuya vida dura solo durante la ejecución del ciclo.

### Comando for ... downto
Fragmento de la siguiente forma también aparecen con cierta frecuencia:
```LenguajeDeLaMateria
k:= m
do k ≥ n --->
	C
	k:= k-1
od
```

por simplicidad, lo reemplazaremos por 

```LenguajeDeLaMateria
For k:= m downto n do C od 
```

Siempre que **k** no se modifique en **C**

---
### Análisis
- Contamos cuantas operaciones elementales realiza el algoritmo
- cuantas sumas, asignaciones, llamadas a funciones, comparaciones, intercambios, etc
- en vez de eso, se elige una operación **Representativa**
- ¿Que es una operación Representativa? Una tal que se repite mas que o tanto como cualquier otra
- Hay que buscar la que **mas se repite**
- Selection_sort contiene un **ciclo**,
- allí debe estar la operación que mas se repite,
- encontramos **una llamada** a la función min_pos_from y **una llamada** al procedimiento swap,
- el procedimiento swap **es constante** (siempre realiza 3 asignaciones elementales),
- la función min_pos_from, en cambio, **tiene un ciclo**,
- nuevamente **allí** debe estar la operación que más se repite,
- encontramos **una comparación** entre elementos de 'a', y **una asignación** (condicionando al resultado de la comparación)
- La **operación que mas se repite es la comparación** entre los elementos de 'a',
- **toda otra operación se repite a lo sumo de manera proporcional** a esa,
- por lo tanto, **la comparación** entre elementos de 'a' **es representativa** del trabajo de la ordenación por selección.
- Esto es habitual: Para medir la eficiencia de los algoritmos de ordenación es habitual considerar el numero de comparaciones entre elementos del arreglo
- Veremos que acceder/modificar una celda de un arreglo es **constante**: su costo no depende de cual es la celda, ni de la longitud del arreglo
---
#### ¿Cuantas comparaciones realiza la ordenación por selección?
Según nuestro Análisis
- Al llamarse a min_pos_from se realiza n-i comparaciones
- Selection_sort llama a min_pos_from(a, i) para i ∈ {1,2,3,....,n-1}
- Por lo tanto, en total son $(n-1) + (n-1) + ... + (n - (n-1))$ comparaciones
- es decir, $(n-1) + (n-2) + ... + 1 = (n * (n-1))/2$ comparaciones
- como $(n * (n-1))/2  =  n²/2 - n/2$, el numero de comparaciones es proporcional a n²
- Conviene utilizar la expresión n² para contestar la pregunta; es más sencillo y da el mismo resultado.