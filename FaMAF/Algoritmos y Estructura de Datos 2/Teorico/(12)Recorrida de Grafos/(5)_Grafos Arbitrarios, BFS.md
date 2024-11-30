##### Si cambiamos la pila por una cola obtenemos BFS
```LenguajeDeLaMateria
proc bfsearch(in G, in/out mark: tmark, in v: V) 
	var q: queue of V 
	empty(q) 
	visit(mark,v) 
	enqueue(q,v) 
	do (¬is_empty(q)) ---> 
		if (existe w ∈ neighbours(first(q)) tal que ¬visited(mark,w)) then 
			visit(mark,w) 
			enqueue(q,w) 
		else 
			dequeue(q) 
		fi 
	od 
end proc
```
---
### BFS, procedimiento principal
```LenguajeDeLaMateria
fun bfs(G=(V,neighbours)) ret mark: tmark 
	init(mark) 
	for (v ∈ V) do 
		if (¬visited(mark,v)) then 
			bfsearch(G, mark, v) 
		fi 
	od 
end fun
```
---
### Ejemplo, BFS
![[ejemplo bfs.png]]