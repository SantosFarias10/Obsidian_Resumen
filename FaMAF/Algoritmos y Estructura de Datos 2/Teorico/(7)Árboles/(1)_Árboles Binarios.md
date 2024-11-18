### Forma de un Árbol
![[Forma_Árbol.png]]
Estructura de datos que consiste en ***nodos*** que almacena un elemento y dos ***Subestructuras***, que a la vez son pueden ser otro nodo o el vacío.
***Constructores***:
- Árbol vacío.
- Nodo consistente de un elemento de algún tipo T y dos árboles.
---
### Botánica y Genealogía
- Un ***Nodo*** es un árbol no vacío.
- Tiene ***Raíz***, ***Subárbol izquierdo*** y ***Subárbol derecho***.
- A los subárboles se los llama también ***Hijos*** (izquierdo y derecho).
- Y a los nodos se les dice ***Padre*** de sus hijos.
- Una ***Hoja*** es un nodo con los dos hijos vacíos.
---
### Más Terminología
- Se usa terminología genealógica como ***Hijo***, ***Padre***, ***Nieto***, ***Abuelo***, ***Hermano***, ***Ancestro***, ***Descendiente***.
- También de la botánica: ***Raíz***, ***Hoja***.
- Se define ***Camino***, ***Altura***, ***Profundidad***, ***Nivel***.
---
### Sobre los Niveles
- En el ***Nivel 1*** hay a los sumo ***1 Nodo***.
- En el ***Nivel 2*** hay a los sumo ***2 Nodo***.
- En el ***Nivel 3*** hay a los sumo ***4 Nodo***.
- En el ***Nivel 4*** hay a los sumo ***8 Nodo***.
- En el ***Nivel i*** hay a los sumo ***$2^{i-1}$ Nodo***.
- En un árbol de altura $n$ hay a lo sumo $2^0 + 2¹ + ... + 2^n-1$ nodos.
- En un árbol "balanceado" la ***altura*** es del orden del $log_2(k)$ donde "k" es el número de nodos.
--- 
### Indicaciones/Posiciones
Podemos recorrer un árbol indicando el camino desde la raíz.
Un ***camino*** lo podemos representar como una secuencia donde cada elemento nos indica si debo ***Bajar*** a la izquierda o a la derecha.
![[Indicaciones.png]]
El camino ***LRLL (IDII)*** nos lleva hasta la hoja ***H***.
El camino ***LRLLL*** no es válido.

---
### Especificación del TAD Árbol Binario
```LenguajeDeLaMateria
type Direction = enumerate
						Left
						Right
				 end enumerate

type Path = List of Direction

spec Tree of T where
```

***Constructors***
```LenguajeDeLaMateria
fun empty_tree() ret t: Tree of T
{- Crea un Árbol vacío -}

fun node(tl: Tree of T, e: T, tr: Tree of T) ret t: Tree of T
{- Crea el nodo con el elemento 'e' y subárboles 'tl' y 'tr' (tree left y tree right xd) -}
```

***Operations***
```LenguajeDeLaMateria
fun is_empty_tree(t: Tree of T) ret b: bool
{- Devuelve True si el árbol es vacío -}

fun root(t: Tree of T) ret e: T
{- Devuelve el elemento que se encuentre en la raíz de 't' -}
{- PRE: not is_empty_tree(t) -}

fun left(t: Tree of T) ret tl: Tree of T
{- Devuelve el subárbol izquierdo de 't' -}
{- PRE: not is_empty_tree(t) -}

fun right(t: Tree of T) ret tr: Tree of T
{- Devuelve el subárbol derecho de 't' -}
{- PRE: not is_empty_tree(t) -}

fun height(t: Tree of T) ret n: nat
{- Devuelve la distancia que hay entre la raíz de 't' y la hoja más profunda -}

fun is_path(t: Tree of T, p: Path) ret b: bool
{- Devuelve True si 'p' es un camino válido en 't' -}

fun subtree_at(t: Tree of T, p: Path) ret t0: Tree of T
{- Devuelve el subárbol que se encuentra al recorrer el camino 'p' en 't' -}

fun elem_at(t: Tree of T, p: Path) ret e: T
{- Devuelve el elemento que se encuentra al recorrer el camino 'p' en 't' -}
{- PRE: is_path(t,p) -}
```
---
### Implementación de árboles binarios
La manera usual de implementar árboles binarios en lenguajes imperativos con punteros es similar a la que usamos cuando implementamos listas enlazadas. Definimos un tipo ***Node*** como una tupla con un elemento y dos punteros a sí mismo:
```LenguajeDeLaMateria
implement Tree of T where

type Node of T = tuple
					left: pointer to (Node of T)
					value: T
					right: Pointer to (Node of T)
				 end tuple

type Tree of T = Pointer to (Node of T)

fun empty_tree() ret t: Tree of T
	t:= null
end fun

fun node(tl: Tree of T, e: T, tr: Tree of T) ret t: Tree of T
	alloc(t)
	t->value:= e
	t->left:= tl
	t->right:= tr
end fun
```