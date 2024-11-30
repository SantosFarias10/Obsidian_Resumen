### Recorrida de árboles finitarios
- Son árboles en los que cada vértice tiene una cantidad finita (pero puede ser variable) de hijos.
- La recorrida in-order deja de tener sentido (habiendo más de dos hijos, ¿en qué momento habría que visitar el elemento que se encuentra en la raíz?).
- Las recorridas pre-order y pos-order (DFS) y BFS siguen teniendo sentido.
---
### Ejemplo de árbol finitario
![[arbol finitario.png]]

---
### Ejemplo, recorrida pre-order
![[finitario pre-order.png]]

---
### Ejemplo, recorrida pos-order
![[finitario pos-order.png]]

---
### Ejemplo, recorrida BFS
![[finitario BFS.png]]

---
### Algoritmo
##### Marca
Cuando se visita un vértice, se marca con un número positivo.
```LenguajeDeLaMateria
type tmark = tuple 
				ord: array[V] of nat 
				cont: nat 
			 end tuple

proc init(out mark: tmark) 
	mark.cont:= 0 
end proc

proc visit(in/out mark: tmark, in v: V) 
	mark.cont:= mark.cont+1 
	mark.ord[v]:= mark.cont 
end proc
```
---
##### Pre-order
Asumimos que un árbol viene dado por su raíz (root) y una función (children) que devuelve (el conjunto o la lista de) los hijos de cada vértice.
```LenguajeDeLaMateria
fun pre_order(G=(V, root, children)) ret mark: tmark 
	init(mark) 
	pre_traverse(G, mark, root) 
end fun

proc pre_traverse(in G, in/out mark: tmark, in v: V) 
	visit(mark,v) 
	for (w ∈ children(v)) do 
		pre_traverse(G, mark, w) 
	od 
end fun
```
---
##### pos-order
```LenguajeDeLaMateria
fun pos_order(G=(V, root, children)) ret mark: tmark 
	init(mark) 
	pos_traverse(G, mark, root) 
end fun

proc pos_traverse(in G, in/out mark: tmark, in v: V) 
	for (w ∈ children(v)) do 
		pos_traverse(G, mark, w) 
	od 
	visit(mark,v) 
end fun
```