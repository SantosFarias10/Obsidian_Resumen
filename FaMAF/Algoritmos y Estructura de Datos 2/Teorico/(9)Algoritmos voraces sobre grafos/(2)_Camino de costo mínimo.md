- Sea G = (V, A) un grafo dirigido con costo no negativos en sus aristas, y sea v ∈ V uno de sus vértices.
- Se busca obtener los caminos de menor costo desde ' v ' hacia cada uno de los demás vértices.
---
### Algoritmo de Dijkstra
#### Idea
- El algoritmo de Dijkstra realiza una secuencia de ' n ' pasos, donde ' n ' es el número de vértices.
- En cada paso, "aprende" el camino de menor costo desde ' v ' a un nuevo vértices.
- A ese nuevo vértice lo pinta de azul.
- Tras esos ' n ' pasos, conoce los costos de los caminos de menor costo a cada uno de los vértices.
---
#### Ejemplo
- Tratemos de entenderlo a través de un ejemplo.
- En cada paso, en los vértices azules anotamos el costo del camino de menor de ' v ' a ese vértice.
- En cada paso, en los vértices blancos anotamos el costo del camino azul de menor costo de ' v ' a ese vértice.
- Un camino azul es uno que a lo sumo tiene al vértice destino blanco, sus otros vértices son azules.
---
#### Paso 1 (a): sabemos lo que cuesta llegar de ' v ' a ' v '
![[paso1.png]]
#### Paso 1 (b): Actualizamos los costos de los caminos azules óptimos
![[paso2.png]]
#### Paso 2 (a): Sabemos lo que cuesta llegar de ' v ' a un nuevo vértices
![[paso2(a).png]]
#### Paso 2 (b): Actualizamos los costos de los caminos azules óptimos
![[paso2(b).png]]
#### Paso 3 (a): Sabemos lo que cuesta llegar de ' v ' a un nuevo vértices
![[paso3(a).png]]
#### Paso 3 (b): Actualizamos los costos de los caminos azules óptimos
![[paso3(b).png]]
#### Paso 4 (a): Sabemos lo que cuesta llegar de ' v ' a un nuevo vértice
![[paso4(a).png]]
#### Paso 4 (b): Actualizamos los costos de los caminos azules óptimos
![[paso4(b).png]]
#### Paso 5 (a): Sabemos lo que cuesta llegar a ' v ' a un nuevo vértice
![[paso5(a).png]]
#### Paso 5 (b): Actualizamos los costos de los caminos azules óptimos
![[paso5(b).png]]

---
### El algoritmo
- Asumiremos que el grafo viene dado por el conjunto de vértices V = {1, 2, . . . , n}
- y los costos por una matriz L : array[1..n,1..n] of Nat,
- que en L[i, j] mantiene el costo de la arista que va de ' i ' a ' j '.
- En caso de no haber ninguna arista de ' i ' a ' j ', L[i, j] = ∞.
- Asumimos L[j, j] = 0.
- El algoritmo funciona también para grafos no dirigidos, simplemente se tiene L[i, j] = L[j, i] para todo par de vértices ' i ' y ' j '.
- La versión que daremos del algoritmo, en vez de hallar el ***camino de costo mínimo*** desde ' v ' hasta cada uno de los demás, halla sólo el costo de dicho camino.
- Es decir, halla el ***costo del camino de costo mínimo*** desde ' v ' hasta cada uno de los demás.
- El resultado estará dado por un arreglo D: array[1..n] of Nat,
- en D[j] devolverá el costo del camino de costo mínimo que va de ' v ' a ' j '.
- El conjunto C es el conjunto de los vértices hacia los que todavía desconocemos cuál es el camino de menor costo.
---
### Algoritmo de Dijkstra
```LenguajeDeLaMateria
fun Dijkstra(L: array[1..n, 1..n] of Nat, v: Nat) ret D: array[1..n] of Nat
	var c: Nat 
	var C: Set of Nat 
	for (i := 1) to n do 
		add(C,i) 
	od 
	elim(C,v) 
	for (j:= 1) to n do 
		D[j]:= L[v,j] 
	od 
	do (not is_empty_set(C)) --->
		c:= “elijo elemento c de C tal que D[c] sea mínimo”
		elim(C,c)
		for j in C do 
			D[j]:= min(D[j],D[c]+L[c,j]) 
		od
	od
end fun
```
- La implementación sigue el esquema de los algoritmos voraces.
- En cada paso, elijo el vértice c al que puedo llegar con menor costo.
- Luego actualizo el costo para llegar a cada uno de los demás vértices, habiendo “aprendido” el mejor camino para ir hasta ' c '.
- Esta actualización se realiza calculando el mínimo entre lo que me costaba antes ir hasta cada vértice ' j ', y lo que me cuesta si voy primero a ' c ', y luego de ahí hasta ' j '.
Modificando levemente la implementación dada, se puede obtener una versión que devuelve el camino hacia cada vértice, no solo su costo. (No se vio en detalle este año).