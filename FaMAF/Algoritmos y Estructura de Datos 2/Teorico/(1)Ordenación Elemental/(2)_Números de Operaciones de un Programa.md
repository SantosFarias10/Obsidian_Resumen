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