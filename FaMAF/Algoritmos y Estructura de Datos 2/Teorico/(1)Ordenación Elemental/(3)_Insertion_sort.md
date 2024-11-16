Insertion_sort = Ordenación por inserción

- ***NO siempre*** es posible contar el ***Numero exacto*** de operaciones.
- Es un Algoritmo que se utiliza, por ejemplo, en juegos de cartas, cuando es necesario mantener un gran numero de cartas en las manos, en forma ordenada,
- cada carta que se levanta de la mesa, se inserta en el lugar correspondiente entre las que ya están en las manos, manteniendolas ordenadas.
---
#### Ejemplo
![[Ejemplo de insertion_sort.png]]

---
## Invariante:
![[invariante de insertion_sort.png]]
- El arreglo "a" es una permutación del original y
- un segmento inicial a[1, i) del arreglo está ordenado.
- (pero en general a[1, i) NO contiene el mínimo del arreglo).
---
### Pseudocódigo
*{Pre: $n≥0 ∧ a = A$}*
```LenguajeDeLaMateria
proc insertion_sort(in/out a: array[1..n] of T)
	for i:= 2 to n do
		insert(a, i)
	od
end proc
```
*{Post: "a" está ordenado y es permutación de A}*

---
### Invariante del procedimiento de inserción
![[invariante del procedimiento de insercion.png]]
- El arreglo "a" es una permutación del original
- a[1, i] sin celdas J esta ordenado, y
- a[J, i] también está ordenado.
---
### Insert
*{Pre: 0 < i ≤ n ∧ a = A}*
```LenguajeDeLaMAteria
proc insert(in/out a: array[1..n] of T, In i: nat)
	var J: nat
	J:= i                              {Inv: Invariante de recién}
	do (J > 1 ∧ a[J] < a[J-1]) ---->
		swap(a, J-1, J)
		J:= J-1
```
*{Post: a[1, i] está ordenado ∧ "a" es permutación de A}*

---
#### Numero de comparaciones e intercambios 
$$\begin{array}{|c|c|c|c|c|}
\hline
\text{si el valor de i es ...} & \text{comparaciones} & & \text{intercambios} & \\
\hline
 & \text{mín} & \text{máx} & \text{mín} & \text{máx} \\
\hline
2 & 1 & 1 & 0 & 1 \\
\hline
3 & 1 & 2 & 0 & 2 \\
\hline
4 & 1 & 3 & 0 & 3 \\
\hline
\vdots & \vdots & \vdots & \vdots & \vdots \\
\hline
n & 1 & n-1 & 0 & n-1 \\
\hline
\text{total insertion\_sort} & n - 1 & \frac{n^2}{2} - \frac{n}{2} & 0 & \frac{n^2}{2} - \frac{n}{2} \\
\hline
\end{array}
$$
#### Ordenación por inserción, casos:
- ***Mejor Caso***: El arreglo esta ordenado. Se hacen n comparaciones y 0 intercambios.
- ***Peor  Caso***: El arreglo esta ordenado al revés. Se hacen $\frac{n^2}{2} - \frac{n}{2}$ comparaciones e intercambios, o sea, del orden $n²$.
- ***Caso Promedio***: El orden es de $n²$.