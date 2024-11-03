
### Recurrencia
- Surgen al analizar algoritmos recursivos, como la Ordenación por Intercalación.
- El conteo de operaciones "copia" la recursión del algoritmo y se vuelve recursivo también.
- Ejemplo: Recurrencia divide y vencerás:
$$
t(n) = 
\begin{cases} 
0 & \text{si } n \in \{0, 1\} \\
t\left(\left\lceil \frac{n}{2} \right\rceil\right) + t\left(\left\lfloor \frac{n}{2} \right\rfloor\right) + n - 1 & \text{si } n > 1 
\end{cases}
$$
- Un ejemplo de divide y vencerás es la [Ordenación por Intercalación](Merge_sort)

## Algoritmo Divide y Vencerás
### Características:
- Hay una solución para los casos sencillos,
- para los complejos, se ***divide*** o ***descompone*** el problema en subproblemas:
	- Cada subproblema es de igual naturaleza que el original,
	- el tamaño del subproblema es una ***fracción*** del original,
	- se resuelve los subproblemas apelando al mismo algoritmo,
- se ***Combina*** esas soluciones para obtener una solución del original.

### Forma general del algoritmo divide y vencerás
```LenguajeDeLaMateria
fun DyV(x) ret y
	if x suficientemente pequeño o simple then 
		y:= ad_hoc(x)
	else
		{- descomponer x en x_1, x_2, ..., x_a -}
		{- x_1 = x sub 1, x_2 = x sub 2, etc xd -}
		for i:= 1 to a do 
			y_i:= DyV(x_i)
		od
		{- combinar y_1, y_2, ..., y_a para obtener la solucion y de x -}
	fi
end fun
```
donde 
- a: numero de llamadas recursivas
- b: relación entre el tamaño de "x" y el de $x_i$, satisface que $|x_i| = \frac{|x|}{b}$
- k: orden de descomponer y combinar es $n^k$
Normalmente los $x_i$ son ***fracciones*** de x: $|x_i| = \frac{|x|}{b}$ para algún b > 1.

##### Ejemplos:
- [Ordenación por Intercalación](Merge_sort):
	- "x simple" = fragmento de arreglo de longitud 0 o 1
	- "descomponer" = partir al medio (b = 2)
	- a = 2
	- "combinar" = intercalar
- [Ordenación Rápida](Quick_sort):
	- "x simple" = fragmento de arreglo de longitud 0 o 1
	- "descomponer" = separar los menores de los mayores (b = 2)
	- a = 2
	- "combinar" = yuxtaponer.

## Conteo
Si queremos contar el costo computacional (números de operaciones) $t(n)$ de la función DyV obtenemos:
$$
t(n) = \begin{cases} c & \text{si la entrada es pequeña o simple} \\ 
a * t\left(\frac{n}{b}\right) + g(n) & \text{en caso contrario} \end{cases}
$$
Si $c$ es una constante que representa el costo computacional de la función ad_hoc y $g(n)$ es el costo computacional de los procesos de descomposición y de combinación.
Esta definición de $t(n)$ es recursiva (como el algoritmo de DyV), se llama ***Recurrencia***. Existen distintos tipos de recurrencia. 
Esta se llama ***Recurrencia divide y vencerás***.

- Si 
$$
t(n) = \begin{cases} c & \text{si la entrada es pequeña o simple} \\ 
a * t\left(\frac{n}{b}\right) + g(n) & \text{en caso contrario} \end{cases}
$$
si $t(n)$ es no decreciente, y si $g(n)$ es del orden $n^k$, entonces
$$
t(n) \text{ es del orden de } \begin{cases} 
      n^{\log_b a} & \text{si } a > b^k \\
      n^k \log n & \text{si } a = b^k \\
      n^k & \text{si } a < b^k
   \end{cases}
$$

### Ejemplo: búsqueda binaria
*{Pre: 1 ≤ lft ≤ n + 1 ∧  0 ≤ rgt ≤ n ∧ a ordenado}*
```LenguajeDeLaMateria
fun binary_search_rec(a: array[1..n] of T, x: T, lft, rgt: nat) ret i: nat
	var mid: nat
	if lft > rgt --->
		i:= 0
	else 
		mid:= (lft + rgt) ÷ 2
		if x < a[mid] --->
			i:= binary_search_rec(a, x, lft, mid - 1)
		else if x = a[mid] --->
			i:= mid
		else if a > a[mid] --->
			i:= binary_search_rec(a, x, mid + 1, rgt)
		fi
	fi
end fun
```
*{Post: (i = 0 => x no está en a[lft, rgt]) ∧ (i ≠0 => x = a[i])}*

##### Función principal de la búsqueda binaria
*{Pre: n ≥ 0}*
```LenguajeDeLaMateria
fun binary_search(a: array[1..n] of T, x: T) ret i: nat
	i:= binary_search_rec(a, x, 1, n)
end fun
```
*{Post: (i = 0 => x no está en a[lft, rgt]) ∧ (i ≠0 => x = a[i])}*

### Analizamos la función de búsqueda binaria
- Sea $t(n)$ = número de comparaciones que hace en el peor caso cuando el arreglo tiene $n$ celdas.
- $$t(n) = \begin{cases} 0 & \text{si n = 0} \\ 
 t\left(\frac{n}{2}\right) + 1 & \text{si n > 0} \end{cases}$$
 - a = 1, b = 2 y k= 0.
 - a = $b^k$
 - $t(n)$ es del ***orden*** de $n^k.log(n)$, es decir, del orden de $log(n)$

### Analicemos otros algoritmos
- [Ordenación por selección](Selection_Sort.md) es del orden de $n²$.
- [Ordenación por inserción](Insertion_sort) es del orden de $n²$ (peor caso y caso medio).
- [Ordenación por intercalación](Merge_sort) es del orden de $n.log_2(n)$.
- [Ordenación rápida](Quick_sort) es del orden de $n.log_2(n)$ (caso medio).
- Búsqueda lineal es del orden de $n$.
- Búsqueda binaria es del orden de $log_2(n)$.

### ¿Cómo comparamos los órdenes de los algoritmos?
- Hay funciones que crecen más rápido que otras (cuando $n$ tiene a +∞).
- Escribiremos $f(n) ⊏ g(n)$ para decir que $g(n)$ ***crece más rápido*** que $f(n)$ 
- Ejemplo:
	- $n.log_2(n) ⊏ n²$.
	- $log_2(n) ⊏ n$.
- Escribiremos $f(n) ≈ g(n)$ para decir que $f(n)$ y $g(n)$ ***crecen al mismo ritmo***.
- Ejemplo: 
	- $$\frac{n²}{2} - \frac{n}{2} ≈ n²$$
	- $45 * n² ≈ n²$

### Algunas condiciones
- No nos interesa las constantes multiplicativas:
	- $\frac{1}{4} * n² ≈ n²$.
	- $4 * n³ ≈ n³$.
	- $1000 * log(n) ≈ log(n)$.
	- $π * n ≈ n$.
- No nos interesa los términos menos significativos, que crecen más lento:
	- $n² + n ≈ n²$
	- $n³ + n² * log_2(n) ≈ n³$
	- $log(n) + 3456 ≈ log(n)$
	- $n + √ n ≈ n$

##### Jerarquía
$$
\begin{align*}
1 & ⊏ log_2(n) ≈ log_3(n) ⊏ n^{0,001} ⊏ n^{1,5} ⊏ n² \\
& ⊏ n⁵ ⊏ n^{100} ⊏ {1,01}^n ⊏ 2^n ⊏ {100}^n ⊏ \\
& ⊏ {100000}^n ⊏ n! ⊏ n^n 
\end{align*}
$$

### Propiedades
- Constantes multiplicativas no afectan.
- Términos de crecimiento despreciable no afectan.
- Sean $a,b > 1$, $log_a(n) ≈ log_b(n)$.
- Sean $f(n)$ > 0 para "casi todo n ∈ N". Entonces:
	- $g(n) ⊏ h(n) <=> f(n).g(n) ⊏ f(n).h(n)$ 
	- $g(n) ≈ h(n) <=> f(n).g(n) ≈ f(n).h(n)$
- Sea $lim_{n->∞} h(n) = ∞$. Entonces:
	- $f(n) ⊏ g(n) => f(h(n)) ⊏ g(h(n))$
	- $f(n) ≈ g(n) => f(h(n)) ≈ g(h(n))$

##### Jerarquía
$$
\begin{align*}
1 & ⊏ \log(\log(\log(n))) ⊏ \log(\log(n)) ⊏ \log(n) ⊏ n^{0.001} ⊏ n \\
& ⊏ n \cdot \log(n) ⊏ n^{1.001} ⊏ n^{100} ⊏ 1.01^n ⊏ n^{100} \cdot 1.01^n \\
& ⊏ 1.02^n ⊏ 100^n ⊏ 10000^n ⊏ (n - 1)! ⊏ n! ⊏ (n + 1)! ⊏ n^n
\end{align*}
$$

### Demostración de la recurrencia DyV
Si queres leer la demostración fíjate en las filminas, es muy larga para copiarla salu2
