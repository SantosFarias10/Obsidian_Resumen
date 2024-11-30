### Ejemplo de Grafo
![[ejemplo de grafo.png]]

---
### Ejemplo, DFS en pre-order
![[ejemplo dfs en pre-order.png]]

---
### Algoritmo
##### Marcas
Como ahora puede haber ciclos, es necesario poder averiguar si un vértice ya fue visitado.
```LenguajeDeLaMateria
proc init(out mark: tmark)
	mark.cont:= 0
	for (v ∈ V) do 
		mark.ord[v]:= 0 
	od
end proc

fun visited(mark: tmark, v: V) ret b: bool 
	b:= (mark.ord[v] 6= 0) 
end fun
```
---
### Algoritmo DFS
```LenguajeDeLaMateria
fun dfs(G=(V, neighbours)) ret mark: tmark 
	init(mark) 
	for (v ∈ V) do 
		if (¬visited(mark,v)) then 
			dfsearch(G, mark, v) 
		fi 
	od 
end fun

proc dfsearch(in G, in/out mark: tmark, in v: V) 
	visit(mark,v) 
	for (w ∈ neighbours(v)) do 
		if (¬visited(mark,w)) then 
			dfsearch(G, mark, w) 
		fi 
	od 
end proc
```
---
### DFS Iterativo
##### Introducimos una pila para evitar recursión
```LenguajeDeLaMateria
proc dfsearch(in G, in/out mark: tmark, in v: V)
	var p: stack of V 
	empty(p) 
	visit(mark,v) 
	push(v,p) 
	do (¬is_empty(p)) --->
		if (existe w ∈ neighbours(top(p)) tal que ¬visited(mark,w)) then 
			visit(mark,w) 
			push(w,p) 
		else 
			pop(p) 
		fi 
	od 
end proc
```