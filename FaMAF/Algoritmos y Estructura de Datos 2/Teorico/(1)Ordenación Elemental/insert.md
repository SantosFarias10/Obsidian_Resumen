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
#### Funciones Auxiliares
- [[swap]]
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