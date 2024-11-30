- Sea G = (V, A) un ***grafo conexo*** no dirigido con un costo no negativo asociado a cada arista.
- Se dice que T ⊆ A es un ***árbol generador*** (intuitivamente, un tendido) si el grafo (V, T) es un conexo y no contiene ciclos.
- Su ***costo*** es la suma de los costos de sus aristas.
- Se busca T tal que su costo sea mínimo.
- El problema de encontrar un árbol generador de costo mínimo tiene numerosas aplicaciones en la vida real.
- Cada vez que se quiera realizar un tendido (eléctrico, telefónico, etc) se quieren unir distintas localidades de modo que requiera el menor costo en instalaciones (por ejemplo, cables) posible.
- Se trata de realizar el tendido siguiendo la traza de un árbol generador de costo mínimo.
---
### Ejemplo
![[Obsidian_Resumen/FaMAF/Algoritmos y Estructura de Datos 2/Teorico/(9)Algoritmos voraces sobre grafos/Ejemplo.png]]
![[ejemplo resuelto.png]]
![[ejemplo resuelto2.png]]
---
### Dos estrategias
Hay dos grandes ideas de cómo resolverlo.
- La de ***Prim***: Se parte desde un vértice origen y se va extendiendo el tendido a partir de ahí:
	- En cada paso se une el tendido ya existente con alguno de los vértices aún no alcanzados, seleccionando la arista de menor costo capaz de incorporar un nuevo vértice.
- La de ***Kruskal***: Se divide el grafo en distintas componentes (originariamente una por cada vértice) y se van uniendo componentes,
	- En cada paso se selecciona la arista de menor costo capaz de unir componentes.
(Este año no se vio en detalle el algoritmo de Kruskal).
---
### Algoritmo de Prim
![[Algoritmo de prim1.png]]
![[algoritmo de prim2.png]]
![[algoritmo de prim3.png]]
![[algoritmo de prim4.png]]
![[algoritmo de prim5.png]]
![[algoritmo de prim7.png]]
---
### Implementación del Algoritmo de Prim
Podemos representar los grafos como una tupla con dos conjuntos: Uno para los vértices y otra para las aristas.
```LenguajeDeLaMateria
type Vertex = Nat     {- Vertice -}

type Edge = tuple            {- Aristas -}
				v1: Vertex
				v2: Vertex
				cost: Nat
			end tuple

type Graph = tuple
				vertices: Set of Vertex    {- Conjunto de Vertices -}
				edges: Set of Edge     {- Conjunto de Aristas -}
			 end tuple

fun Prim(G: Graph, k: Vertex) ret T: Set of Edge
	var c: Edge
	var C: Set of Vertex
	
	C:= copy_set(G.vertices)
	elim(C, k)
	T:= empty_set()
	
	do(not is_empty_set(C)) --->
		c:= "Selecciono arista de costo mínimo tal que c.v1 ∈ C y c.v2 not∈ C, ó c.v2 ∈ C y c.v1 not∈ C"
		if(member(c.v1, C)) then    {- ¿member? -}
			elim(C, c.v1)
		else
			elim(C, c.v2)
			add(T, c)
		fi
	od
end fun
```