### Problema de la moneda
- Sean 0 ≤ i ≤ n y 0 ≤ j ≤ k
- definimos `cambio(i, j)` = "menor número de monedas necesarias para pagar exactamente el monto `j` con denominaciones $d_1,d_2,...,d_n$".
- $$\text{cambio}(i, j) =
\begin{cases}
0 & j = 0 \\
\infty & j > 0 \land i = 0 \\
\text{cambio}(i-1, j) & d_i > j > 0 \land i > 0 \\
\min(\text{cambio}(i-1, j), 1 + \text{cambio}(i, j-d_i)) & j \geq d_i > 0 \land i > 0
\end{cases}$$
- Es exponencial.
---
### Problema de la Mochila
- Sean 0 ≤ i ≤ n y 0 ≤ j ≤ W,
- definimos `mochila(i, j)` = "mayor valor alcanzable sin exceder la capacidad `j` con objetos $1, 2, ..., i$"
- $$m(i, j) =
\begin{cases}
0 & j = 0 \\
0 & j > 0 \land i = 0 \\
m(i-1, j) & w_i > j > 0 \land i > 0 \\
\max(m(i-1, j), v_i + m(i-1, j-w_i)) & j \geq w_i > 0 \land i > 0
\end{cases}$$
- Es exponencial.
----
### Problema de los caminos de costo mínimo entre cada par de vértices
- Sean 1 ≤ i, j ≤ n y 0 ≤ k ≤ n,
- definimos `camino(k, i, j)` = "menor costo posible para caminos de `i` a `j` cuyos vértices intermedios se encuentran en el conjunto {1,. . . ,k}"
- $$c(k, i, j) =
\begin{cases}
L[i, j] & k = 0 \\
\min(c(k-1, i, j), c(k-1, i, k) + c(k-1, k, j)) & k \geq 1
\end{cases}$$
- Es exponencial ($3^n$).