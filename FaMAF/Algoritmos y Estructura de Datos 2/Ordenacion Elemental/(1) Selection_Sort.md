### Idea
- Es el algoritmo de ordenación mas sencillo (pero no el mas rápido)
- **Selecciona** el menor de todos, lo intercambia con el elemento que se encuentra en la primera posición 
- **_Selecciona_** el menor de todos **_los restantes_**, lo intercambia con el que se encuentra en el segundo lugar
- **_Selecciona_** el menor de todos **_los restantes**_, lo intercambia con el que se encuentra en tercer lugar 
- ... (en cada uno de estos pasos ordena un elemento) ... 
- hasta terminar
### Ejemplo

![[EjemploSelection_sort.png]]

### Invariante:

![[InvarianteSelection_sort.png]]

- El Arreglo "a" es una permutación del original,
- Un segmento inicial a[1,i) 

### Pseudocódigo

*{Pre: $n≥0 ∧ a = A$ }*
``` LenguajeDeLaMateria
Proc Selection_sort (in/out a: array [1..n] of T)
	var i, minp: nat
	i := 1          {Inv: Invariante de recién}
	do i<n --->
		minp := min_pos_from(a, i)
		swap(a, i, minp)
		i  := i+1
	od
end proc
```
*{Post: 'a' está ordenado y es permutación de A}*
#### Funciones Auxiliares
- [min_pos_from](FuncionDeSeleccion.md)
- [swap](swap.md)

Con el [[Comando For]]
```LenguajeDeLaMateria
Proc Selection_sort(in/out a: array [1..n] of T)
	var minp: nat
	for i:= 1 to n-1 do
		minp:= min_pos_from(a, i)
		swap(a, i, minp)
	od
end proc
```
#### Funciones Auxiliares
- [min_pos_from](FuncionDeSeleccion.md)
- [swap](swap.md)

### Invariante de la función de selección 

![[Inariante de la funcion de seleccion.png]]

- Invariante anterior, y
- El mínimo del segmento a[i,j] está en la posición minp

### ¿Cuantas comparaciones realiza la ordenación por selección?

Según nuestro [[Análisis]]
- Al llamarse a [min_pos_from](FuncionDeSeleccion.md)(a, i) se realiza n-i comparaciones
- Selection_sort llama a [min_pos_from](FuncionDeSeleccion.md)(a, i) para i ∈ {1,2,3,....,n-1}
- Por lo tanto, en total son $(n-1) + (n-1) + ... + (n - (n-1))$ comparaciones
- es decir, $(n-1) + (n-2) + ... + 1 = (n * (n-1))/2$ comparaciones
- como $(n * (n-1))/2  =  n²/2 - n/2$, el numero de comparaciones es proporcional a n²
- Conviene utilizar la expresión n² para contestar la pregunta; es más sencillo y da el mismo resultado

### ¿Cual es el [[Numero de operaciones]] de Selection_sort?
