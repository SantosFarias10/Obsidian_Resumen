Selection_sort = Ordenación por selección
### Idea
- Es el algoritmo de ordenación mas sencillo (pero no el mas rápido)
- **Selecciona** el menor de todos, lo intercambia con el elemento que se encuentra en la primera posición 
- **_Selecciona_** el menor de todos **_los restantes_**, lo intercambia con el que se encuentra en el segundo lugar
- **_Selecciona_** el menor de todos **_los restantes**_, lo intercambia con el que se encuentra en tercer lugar 
- ... (en cada uno de estos pasos ordena un elemento) ... 
- hasta terminar
#### Ejemplo
![[EjemploSelection_sort.png]]

### Invariante:
![[InvarianteSelection_sort.png]]
- El Arreglo "a" es una permutación del original,
- Un segmento inicial a[1,i) del arreglo esta ordenado,
- dicho segmento contiene los elementos mínimos del arreglo.
 
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

#### ¿Cuantas comparaciones realiza la ordenación por selección?
Según nuestro [[Análisis]]
- Al llamarse a [min_pos_from](FuncionDeSeleccion.md)(a, i) se realiza n-i comparaciones
- Selection_sort llama a [min_pos_from](FuncionDeSeleccion.md)(a, i) para i ∈ {1,2,3,....,n-1}
- Por lo tanto, en total son $(n-1) + (n-1) + ... + (n - (n-1))$ comparaciones
- es decir, $(n-1) + (n-2) + ... + 1 = (n * (n-1))/2$ comparaciones
- como $(n * (n-1))/2  =  n²/2 - n/2$, el numero de comparaciones es proporcional a n²
- Conviene utilizar la expresión n² para contestar la pregunta; es más sencillo y da el mismo resultado

#### ¿Cual es el [Numero de Comparaciones](NumeroDeOperaciones.md) de Selection_sort?
$$ \begin{align*} \text{ops}(\text{selection\_sort}(a)) &= \text{ops}\left(\text{for } i := 1 \text{ to } n \text{ do minp := min\_pos\_from}\ldots; \text{ swap}\ldots \text{ od}\right) \\ &= \sum_{i=1}^{n} \text{ops}\left(\text{minp := min\_pos\_from}(a, i); \text{ swap}(a, i, \text{minp})\right) \\ &= \sum_{i=1}^{n} \left(\text{ops(minp := min\_pos\_from}(a, i)) + \text{ops(swap}(a, i, \text{minp}))\right) \\ &= \sum_{i=1}^{n} \text{ops(minp := min\_pos\_from}(a, i)) \\ &= \sum_{i=1}^{n} \text{ops(min\_pos\_from}(a, i)) \\ &= \sum_{i=1}^{n} \text{ops}\left(\text{minp := i; for } j := i+1 \text{ to } n \text{ do if } \ldots \text{ fi od}\right) \\ &= \sum_{i=1}^{n} \left(\text{ops(minp := i)} + \text{ops}\left(\text{for } j := i+1 \text{ to } n \text{ do if } \ldots \text{ fi od}\right)\right) \\ &= \sum_{i=1}^{n} \text{ops}\left(\text{for } j := i+1 \text{ to } n \text{ do if } \ldots \text{ fi od}\right) \\ &= \sum_{i=1}^{n} \sum_{j=i+1}^{n} \text{ops}\left(\text{if } a[j] < a[\text{minp}] \text{ then minp := j if}\right) \\ &= \sum_{i=1}^{n} \sum_{j=i+1}^{n} \left(\text{ops}(a[j] < a[\text{minp}]) + \text{ops(minp := j)}\right) \circ \text{ops(skip)} \\ &= \sum_{i=1}^{n} \sum_{j=i+1}^{n} \text{ops}(a[j] < a[\text{minp}]) \\
&= \sum_{i=1}^{n} \sum_{j=i+1}^{n} 1 \\ &= \sum_{i=1}^{n} (n - i) \\ &= \sum_{i=0}^{n-1} i \\ &= \frac{n \cdot (n-1)}{2} \\ &= \frac{n^2}{2} - \frac{n}{2}
\end{align*} $$


#### [Numero de Intercambio](NumeroDeOperaciones.md) de Selection_sort
$$ \begin{align*} \text{ops}(\text{selection\_sort}(a)) &= \text{ops}\left(\text{for } i := 1 \text{ to } n \text{ do minp := min\_pos\_from} \ldots ; \text{swap} \ldots \text{ od}\right) \\ &= \sum_{i=1}^{n} \text{ops}(\text{minp := min\_pos\_from}(a, i); \text{swap}(a, i, \text{minp})) \\ &= \sum_{i=1}^{n} \left(\text{ops}(\text{minp := min\_pos\_from}(a, i)) + \text{ops}(\text{swap}(a, i, \text{minp}))\right) \\ &= \cdots = \sum_{i=1}^{n} (0 + \text{ops}(\text{swap}(a, i, \text{minp}))) \\ &= \sum_{i=1}^{n} \text{ops}(\text{swap}(a, i, \text{minp})) \\ &= \sum_{i=1}^{n} 1 \\ &= n \end{align*} $$

#### Conclusión de los Ejemplos:
- El numero de comparaciones de Selection_sort: $\frac{n^2}{2} - \frac{n}{2}$
- El numero de intercambios de Selection_sort: $n$
- Esto significa que la operación de ***Intercambio no es Representativa*** del comportamiento de Selection_sort, ya que el numero de comparaciones crece mas que proporcionalmente respecto a los intercambios.
- Por otro lado, podemos contar las operaciones de manera ***Exacta***.