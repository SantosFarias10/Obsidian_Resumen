- Método para transformar una definición recursiva en iterativa
- a través de la confección de una ***Tabla de valores***.
- Objetivo: Evitarla reiteración de cómputos.
- Ejemplo: Definición recursiva de la secuencia de Fibonacci.
---
### Secuencia de Fibonacci
- $$
f_n =
\begin{cases} 
n & \text{si } n \leq 1, \\
f_{n-1} + f_{n-2} & \text{si } n > 1.
\end{cases}
$$
- Esta función recursiva es exponencial.
- La razón, el cálculo de $f_n$ lleva a calcular:
	- 2 veces $f_{n-2}$,
	- 3 veces $f_{n-3}$,
	- 5 veces $f_{n-4}$,
	- etc.

![[secuencia de fibonacci.png]]

---
### ¿Como podemos evitar tantos recálculos?
- Llevando una tabla de valores calculados.
- Comenzando desde los casos bases.
- Sea `f` un arreglo de 0 a `n`
	- `f[0]:= 0`,
	- `f[1]:= 1`,
	- `f[2]:= f[1] + f[0]`,
	- `f[3]:= f[2] + f[1]`,
	- etc.
---
### Fibonacci a través de una tabla
```LenguajeDeLaMateria
{- PRE: n > 1 -}
fun fib(n: Nat) ret r: Nat
	var f: array[0..n] of Nat
	f[0]:= 0
	f[1]:= 1
	for i:= 2 to n do
		f[i]:= f[i - 1] + f[i - 2]
	od
end fun
```
Este algoritmo es ¡¡¡***LINEAL***!!!

---
## Problema de la moneda 
#### Backtracking
Vimos la definición 
$$
\text{cambio}(i, j) =
\begin{cases}
0 & j = 0 \\
\infty & j > 0 \land i = 0 \\
\text{cambio}(i-1, j) & d_i > j > 0 \land i > 0 \\
\min(\text{cambio}(i-1, j), 1 + \text{cambio}(i, j-d_i)) & j \geq d_i > 0 \land i > 0
\end{cases}
$$
que puede ser exponencial debido a que tiene dos llamadas recursivas en el último caso.

---
#### Confección de una tabla
- Habiendo dos parámetros, la tabla será una matriz en vez de un vector como en el caso de Fibonacci.
- Los casos bases corresponden al llenado de la primera columna y primera fila de la matriz.
- Como todas las llamadas recursivas se realizan decrementando el "Parámetro `i`" o manteniendolo igual pero en ese caso decrementando el "Parámetro `j`", se propone el siguiente método de llenado de la tabla:
	- Fila por fila, desde la primera a la última, de modo de que el valor correspondiente a `cambio(i − 1, j)` ya esté computado al calcular el valor correspondiente a `cambio(i, j)`.
	- Dentro de cada fila, desde la primera columna hasta la última, de modo de que el valor correspondiente a `cambio(i, j - d_i)` ya esté computado al calcular `cambio(i, j)`.
---
#### Programación Dinámica
```LenguajeDeLaMateria
fun cambio(d:array[1..n] of Nat, k: Nat) ret r: Nat
	var cam: array[0..n,0..k] of Nat
	for i:= 0 to n do 
		cam[i,0]:= 0 
	od
	for j:= 1 to k do 
		cam[0,j]:= ∞ 
	od
	for i:= 1 to n do
		for j:= 1 to k do
			if (d[i] > j) then 
				cam[i,j]:= cam[i-1,j]
			else
				cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]])
			fi
		od
	od
	r:= cam[n, k]
end fun
```
---
#### Ejemplo con denominaciones $d_1 = 4, d_2 = 2$ y $d_3 = 7$
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
1 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 0 to n do 
	cam[i,0]:= 0 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
1 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for j:= 1 to k do 
	cam[0,j]:= ∞ 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ & ∞ \\ \hline
1 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
##### i = 1
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		---> if (d[i] > j) then <--- 
			cam[i,j]:= cam[i-1,j] 
		else 
			cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) 
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & \textcolor{blue}{\infty} & \textcolor{blue}{\infty} & \textcolor{blue}{\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \textcolor{blue}\infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & \textcolor{blue}0 & \infty & \infty & \infty &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \textcolor{blue}\infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \textcolor{blue}\infty & \infty & \infty & 1 &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \textcolor{blue}\infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \textcolor{blue}\infty & \infty & 1 & \infty &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \textcolor{blue}\infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \textcolor{blue}\infty & 1 & \infty & \infty &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \textcolor{blue}\infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & \textcolor{blue}1 & \infty & \infty &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \textcolor{blue}\infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \textcolor{blue}\infty & \infty & \infty & 2 &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
##### i = 2
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		---> if (d[i] > j) then <--- 
			cam[i,j]:= cam[i-1,j] 
		else 
			cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) 
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \textcolor{blue}\infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \textcolor{blue}\infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & \textcolor{blue}0 & \infty &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \textcolor{blue}\infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \textcolor{blue}\infty & 1 &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \infty & 2 & \infty & 3 & \infty & 3 & \infty & 4 & \infty & 4 \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
##### i = 3
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		---> if (d[i] > j) then <--- 
			cam[i,j]:= cam[i-1,j] 
		else 
			cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) 
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \infty & 2 & \infty & 3 & \infty & 3 & \infty & 4 & \infty & 4 \\ \hline
3 & 0 & \infty & 1 & \infty & 1 & \infty & 2 &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \textcolor{blue}\infty & 2 & \infty & 3 & \infty & 3 & \infty & 4 & \infty & 4 \\ \hline
3 & \textcolor{blue}0 & \infty & 1 & \infty & 1 & \infty & 2 &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \infty & \textcolor{blue}2 & \infty & 3 & \infty & 3 & \infty & 4 & \infty & 4 \\ \hline
3 & 0 & \textcolor{blue}\infty & 1 & \infty & 1 & \infty & 2 & 1 &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \infty & 2 & \textcolor{blue}\infty & 3 & \infty & 3 & \infty & 4 & \infty & 4 \\ \hline
3 & 0 & \infty & \textcolor{blue}1 & \infty & 1 & \infty & 2 & 1 & 2 &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		---> cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]]) <---
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \infty & 2 & \infty & \textcolor{blue}3 & \infty & 3 & \infty & 4 & \infty & 4 \\ \hline
3 & 0 & \infty & 1 & \textcolor{blue}\infty & 1 & \infty & 2 & 1 & 2 & 2 &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]])
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \infty & 2 & \infty & 3 & \infty & 3 & \infty & 4 & \infty & 4 \\ \hline
3 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \textcolor{blue}1 & 2 & 2 & 3 & 2 & 3 & 3 &  &  &  \\ \hline
\end{array}
$$
---
```LenguajeDeLaMateria
for i:= 1 to n do 
	for j:= 1 to k do 
		if (d[i] > j) then  
			cam[i,j]:= cam[i-1,j] 
		else 
		cam[i,j]:= min(cam[i-1,j],1+cam[i,j-d[i]])
		fi 
	od 
od
```
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & {\infty} & {\infty} & {\infty} & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty & \infty \\ \hline
1 & 0 & \infty & \infty & \infty & 1 & \infty & \infty & \infty & 2 & \infty & \infty & \infty & 3 & \infty & \infty & \infty & 4 \\ \hline
2 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & \infty & 2 & \infty & 3 & \infty & 3 & \infty & 4 & \infty & 4 \\ \hline
3 & 0 & \infty & 1 & \infty & 1 & \infty & 2 & 1 & 2 & 2 & 3 & 2 & 3 & 3 & 2 & 3 & 3 \\ \hline
\end{array}
$$
---
## Problema de la mochila
#### Backtracking
Vimos la definición
$$
m(i, j) =
\begin{cases}
0 & j = 0 \\
0 & j > 0 \land i = 0 \\
m(i-1, j) & w_i > j > 0 \land i > 0 \\
\max(m(i-1, j), v_i + m(i-1, j-w_i)) & j \geq w_i > 0 \land i > 0
\end{cases}
$$
que puede ser exponencial debido a que tiene dos llamadas recursivas en el último caso.

---
#### Confección de una tabla
- Habiendo dos parámetros, la tabla será nuevamente una matriz.
- Los casos base corresponden al llenado de la primera columna y primera fila de la matriz.
- Como todas las llamadas recursivas se realizan decrementando el “parámetro `i`”, la única condición necesaria para el llenado de la tabla es proceder fila por fila, no importa el orden de llenado dentro de cada fila.
---
#### Programación Dinámica
```LenguajeDeLaMateria
fun mochila(v:array[1..n] of valor, w:array[1..n] of Nat, W: Nat) ret r: valor
	var moch: array[0..n,0..W] of valor
	for i:= 0 to n do 
		moch[i,0]:= 0 
	od
	for j:= 1 to W do 
		moch[0,j]:= 0 
	od
	for i:= 1 to n do
		for j:= 1 to W do
			if (w[i] > j) then 
				moch[i,j]:= moch[i-1,j]
			else 
				moch[i,j]:= max(moch[i-1,j],v[i]+moch[i-1,j-w[i]])
			fi
		od
	od
end fun
```
---
#### Ejemplo con valores $v_1 = 3, v_2 = 2, v_3 = 3, v_4 = 2, w_1 = 8, w_2 = 5, w_3 = 7$ y $w_4 = 3$
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
1 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \textcolor{blue}3 &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & \textcolor{blue}3 &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & \textcolor{blue}3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & \textcolor{blue}3 &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \textcolor{blue}3 & 3 & 3 & 3 & 3 & \textcolor{blue}3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & \textcolor{blue}5 &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 2 & 2 & \textcolor{blue}2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & \textcolor{blue}3 &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & \textcolor{blue}0 & 2 & 2 & 2 & 3 & 3 & 3 & \textcolor{blue}3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & \textcolor{blue}3 &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & \textcolor{blue}3 & 3 & 3 & 3 & 3 & 5 & 5 & \textcolor{blue}5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & \textcolor{blue}6 &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 & 0 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 & 0 & 0 & 2 & 2 & 2 & 2 &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 & 0 & 0 & 2 & 2 & 2 & 2 & 3 & 4 & 4 &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 & 0 & 0 & 2 & 2 & 2 & 2 & 3 & 4 & 4 & 5 & 5 & 5 & 5 & 5 &  &  \\ \hline
\end{array}
$$
---
#### Ejemplo con valores $v_1 = 3, v_2 = 2, v_3 = 3, v_4 = 2, w_1 = 8, w_2 = 5, w_3 = 7$ y $w_4 = 3$
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
1 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 &  &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \textcolor{blue}3 &  &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & \textcolor{blue}3 &  &  &  &  &  &  &  \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 &  &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & \textcolor{blue}3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & \textcolor{blue}3 &  &  &  &  &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 &  &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & \textcolor{blue}3 & 3 & 3 & 3 & 3 & \textcolor{blue}3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & \textcolor{blue}5 &  &  &  \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 &  &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & \textcolor{blue}0 & 0 & 0 & 0 & 0 & 2 & 2 & \textcolor{blue}2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & \textcolor{blue}3 &  &  &  &  &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & \textcolor{blue}0 & 2 & 2 & 2 & 3 & 3 & 3 & \textcolor{blue}3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & \textcolor{blue}3 &  &  &  &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 &  &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & \textcolor{blue}3 & 3 & 3 & 3 & 3 & 5 & 5 & \textcolor{blue}5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & \textcolor{blue}6 &  \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 & 0 & 0 &  &  &  &  &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 & 0 & 0 & 2 & 2 & 2 & 2 &  &  &  &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 & 0 & 0 & 2 & 2 & 2 & 2 & 3 & 4 & 4 &  &  &  &  &  &  &  \\ \hline
\end{array}
$$
---
$$
\begin{array}{|c|*{17}{c|}}
\hline
 & 0 & 1 & 2 & 3 & 4 & 5 & 6 & 7 & 8 & 9 & 10 & 11 & 12 & 13 & 14 & 15 & 16 \\ \hline
0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 \\ \hline
1 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 0 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 & 3 \\ \hline
2 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 5 \\ \hline
3 & 0 & 0 & 0 & 0 & 0 & 2 & 2 & 3 & 3 & 3 & 3 & 3 & 5 & 5 & 5 & 6 & 6 \\ \hline
4 & 0 & 0 & 0 & 2 & 2 & 2 & 2 & 3 & 4 & 4 & 5 & 5 & 5 & 5 & 5 & 7 & 7 \\ \hline
\end{array}
$$
