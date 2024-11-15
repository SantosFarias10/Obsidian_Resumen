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
- Conviene utilizar la expresión n² para contestar la pregunta; es más sencillo y da el mismo resultado
---


### Numero de comparaciones
-  Una vez que uno sabe que **operaciones** quiere contar, debe imaginar una ejecución arbitraria, genérica del programa intentando contar el numero de veces que esa ejecución arbitraria realizara **dicha ejecución** 

## Secuencia de Comandos
- Una secuencia de comandos se ejecuta de manera secuencial, del primero al ultimo
- La secuencia se puede escribir horizontalmente

$C1; C2; C3; ...; Cn$

- o verticalmente

$$\begin{align*}
C(n) \\
C(n+1) \\
........ \\
C(m)
\end{align*}$$

- Para contar cuantas veces se ejecuta **la operacion** entonces, se cuenta cuantas veces se ejecuta en el primero, cuantas en el segundo, etc. Y luego se suman los números obtenidos:

 $ops(C1; C2; C3; ...; Cn) = ops(C1) + ops(C2) + ops(C3) + ... + ops(Cn)$

### Comando skip
- el comando **skip** equivale a una secuencia vacía:
- $ops(skip) = 0$

### Comando For
- El comando **for** $k:= n$ **to** m **do** C(k) **od**  "equivale" también a una secuencia:
- **for** $k:= n$ **to** m **do** C(k) **od** "equivale" a

$$\begin{align*}
C(n) \\
C(n+1) \\
........ \\
C(m)
\end{align*}$$

- De esta "equivalencia" resulta 

$ops$(**for** $k:= n$ **to** m **do** C(k) **od**) $= ops(C(n)) + ops(C(n+1)) + ... + ops(C(m))$

- Que también se puede escribir 

$ops$(**for** k:= n **to** m **do** C(k) **od**) = $\sum_{k=n}^{m} ops(C(k))$

- La ecuación solamente vale cuando **no hay interés en contar las operaciones que involucran el indice $k$ implícitas en el for**: inicialización, comparación con la cota m, incremento; ni el computo de los limites $n$ y $m$. Por eso escribimos "equivale" entre comillas.

### Comando condicional if
- El comando **if** $b$ **then** $C$ **else** $D$ **fi** se ejecuta la condición $b$ y luego, en función del valor de verdad que se obtenga, ejecutando $C$ (caso de verdad) o $D$ (caso falso)
- Para contar cuantas veces se ejecuta **la operacion**, entonces, se cuenta cuantas veces se la ejecuta durante la evaluación de b$ y luego cuantas en la ejecución de $C o $D$

$$ \text{ops(if } b \text{ then } C \text{ else } D \text{ fi)} = \begin{cases} \text{ops}(b) + \text{ops}(C), & \text{caso } verdadero \\ \text{ops}(b) + \text{ops}(D), & \text{caso } falso \end{cases} $$

### Asignación
- El comando $x:= e$ se ejecuta la expresión $e$ y modificando la posición de memoria de donde se aloja la variable $x$ con el valor de  $e$

$$ \text{ops(x := e)} = \begin{cases} \text{ops}(e) +\text{1}, & \text{Si se desea contar la asignacion o las modificaciones de memoria } \\ \text{ops}(e), & \text{caso contrario } \end{cases} $$

- Tener en cuenta que la evaluación de $e$ puede implicar la llamada a funciones auxiliares cuyas operaciones deben ser también contadas.

### El Ciclo do
- El ciclo *do b ---> C od* (o equivalentemente *while b do C od*) se ejecuta evaluando la condición b, y dependiendo de si su valor es ***Verdadero*** o ***Falso*** se continua de la siguiente manera:
	- Si su valor es ***Falso***, la ejecución termina inmediatamente.
	- Si su valor fue ***Verdadero***, la ejecución continua con la ejecución del cuerpo C del ciclo, y luego de eso se vuelve a ejecutarse todo el ciclo nuevamente.
- O sea que su ejecución es una secuencia de evaluaciones de la condición "b" y ejecuciones del cuerpo C que finaliza con la primera evaluación de "b" que de ***Falso***
- Es decir, la ejecución del ciclo *do b ---> C od* "equivale" a la ejecución de:

```LenguajeDeLaMateria
if b then C
	if b then C
		if b then C
			... indefinidamente xd
		else skip
	else skip
else skip
```

- $$\text{ops}(\textbf{do } \, b \rightarrow C \, \textbf{od}) = \text{ops}(b) + \sum_{k=1}^{n} d_k$$
	- $n$ es el numero de veces que se ejecuta el cuerpo do
	- $d_k$ es el numero de operaciones que realizan la k-esima ejecución del cuerpo C del ciclo y la subsiguiente evaluación de la condición o guarda $b$.

### Numero de operaciones de una expresión 
- Similares ecuaciones se pueden obtener para la evaluación de expresiones
- Por ejemplo para evaluar la expresión $e<f$, primero se evalúa la expresión $e$, luego se evalúa la expresión $f$ y luego se comparan dichos valores

$$ \text{ops(} e < f )= \begin{cases} \text{ops}(e) + \text{ops}(f) + 1, & \text{Si se encuentran comparaciones }\\ \text{ops}(b) + \text{ops}(D), & \text{caso Contraio}\end{cases} $$
---
#### ¿Cual es el Numero de Comparaciones de Selection_sort?
$$ \begin{align*} \text{ops}(\text{selection\_sort}(a)) &= \text{ops}\left(\text{for } i := 1 \text{ to } n \text{ do minp := min\_pos\_from}\ldots; \text{ swap}\ldots \text{ od}\right) \\ &= \sum_{i=1}^{n} \text{ops}\left(\text{minp := min\_pos\_from}(a, i); \text{ swap}(a, i, \text{minp})\right) \\ &= \sum_{i=1}^{n} \left(\text{ops(minp := min\_pos\_from}(a, i)) + \text{ops(swap}(a, i, \text{minp}))\right) \\ &= \sum_{i=1}^{n} \text{ops(minp := min\_pos\_from}(a, i)) \\ &= \sum_{i=1}^{n} \text{ops(min\_pos\_from}(a, i)) \\ &= \sum_{i=1}^{n} \text{ops}\left(\text{minp := i; for } j := i+1 \text{ to } n \text{ do if } \ldots \text{ fi od}\right) \\ &= \sum_{i=1}^{n} \left(\text{ops(minp := i)} + \text{ops}\left(\text{for } j := i+1 \text{ to } n \text{ do if } \ldots \text{ fi od}\right)\right) \\ &= \sum_{i=1}^{n} \text{ops}\left(\text{for } j := i+1 \text{ to } n \text{ do if } \ldots \text{ fi od}\right) \\ &= \sum_{i=1}^{n} \sum_{j=i+1}^{n} \text{ops}\left(\text{if } a[j] < a[\text{minp}] \text{ then minp := j if}\right) \\ &= \sum_{i=1}^{n} \sum_{j=i+1}^{n} \left(\text{ops}(a[j] < a[\text{minp}]) + \text{ops(minp := j)}\right) \circ \text{ops(skip)} \\ &= \sum_{i=1}^{n} \sum_{j=i+1}^{n} \text{ops}(a[j] < a[\text{minp}]) \\
&= \sum_{i=1}^{n} \sum_{j=i+1}^{n} 1 \\ &= \sum_{i=1}^{n} (n - i) \\ &= \sum_{i=0}^{n-1} i \\ &= \frac{n \cdot (n-1)}{2} \\ &= \frac{n^2}{2} - \frac{n}{2}
\end{align*} $$

---
#### Numero de Intercambio de Selection_sort
$$ \begin{align*} \text{ops}(\text{selection\_sort}(a)) &= \text{ops}\left(\text{for } i := 1 \text{ to } n \text{ do minp := min\_pos\_from} \ldots ; \text{swap} \ldots \text{ od}\right) \\ &= \sum_{i=1}^{n} \text{ops}(\text{minp := min\_pos\_from}(a, i); \text{swap}(a, i, \text{minp})) \\ &= \sum_{i=1}^{n} \left(\text{ops}(\text{minp := min\_pos\_from}(a, i)) + \text{ops}(\text{swap}(a, i, \text{minp}))\right) \\ &= \cdots = \sum_{i=1}^{n} (0 + \text{ops}(\text{swap}(a, i, \text{minp}))) \\ &= \sum_{i=1}^{n} \text{ops}(\text{swap}(a, i, \text{minp})) \\ &= \sum_{i=1}^{n} 1 \\ &= n \end{align*} $$
---
#### Conclusión de los Ejemplos:
- El numero de comparaciones de Selection_sort: $\frac{n^2}{2} - \frac{n}{2}$
- El numero de intercambios de Selection_sort: $n$
- Esto significa que la operación de ***Intercambio no es Representativa*** del comportamiento de Selection_sort, ya que el numero de comparaciones crece mas que proporcionalmente respecto a los intercambios.
- Por otro lado, podemos contar las operaciones de manera ***Exacta***.