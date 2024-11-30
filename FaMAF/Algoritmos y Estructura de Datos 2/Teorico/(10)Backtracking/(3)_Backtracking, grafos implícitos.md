### Problema de la moneda
$$
\text{cambio}(i, j) =
\begin{cases}
0 & j = 0 \\
\infty & j > 0 \land i = 0 \\
\text{cambio}(i-1, j) & d_i > j > 0 \land i > 0 \\
\min(\text{cambio}(i-1, j), 1 + \text{cambio}(i, j-d_i)) & j \geq d_i > 0 \land i > 0
\end{cases}
$$
Podemos dibujar un árbol indicando en cada vértice la llamada a la función `cambio(i, j)` indicando el resultado obtenido hasta ese momento. El vértice (i , j, x) indica que llamo a `cambio(i, j)` y el resultado hasta ese momento es ' x '.

---
### Grafo implícito 
#### Ejemplo $d_1 = 1, d_2 = 10, d_3 = 25, k = 30$
![[grafo implícit.png]]
#### Definición General
- Desde el vértice (i , j, x), si $i, j > 0$ y $d_i < j$ existe una única arista al vértice (i - 1, j, x).
- En cambio si $j ≤ d_i$ existen dos aristas:
	- una a (i - 1, j, x)
	- y otra a (i, j - $d_i$, x + 1).
- La raíz es el vértice (n, k, 0).