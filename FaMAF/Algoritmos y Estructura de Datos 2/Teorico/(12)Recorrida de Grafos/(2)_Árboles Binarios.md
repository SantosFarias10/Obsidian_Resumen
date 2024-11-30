### Recorrida de árboles binarios
Un caso de grafo sencillo que ya han visto es el de árbol binario. Se han visto 3 maneras de ***recorrerlo***:
- ***pre-order***: Se ***visita*** primero el elemento que se encuentra en la raíz, luego se ***recorre*** el subárbol izquierdo y finalmente se ***recorre*** el subárbol derecho.
- ***in-order***: Se ***recorre*** el subárbol izquierdo, luego se ***visita*** el elemento que se encuentra en la raíz y finalmente se ***recorre*** el subárbol derecho.
- ***pos-order***: Se ***recorre*** el subárbol izquierdo, luego el derecho y finalmente se visita el ***elemento*** que se encuentra en la raíz.
---
### Ejemplo de árboles binarios
![[Ejemplo arboles binarios.png]]

---
##### Ejemplo, recorrida pre-order
![[Ejemplo recorrida pre-order1.png]]

---
![[Ejemplo recorrida pre-order2.png]]

---
![[jemplo recorrida pre-order3.png]]

---
![[Ejemplo recorrida pre-order4.png]]

---
![[Ejemplo recorrida pre-order5.png]]

---
![[Ejemplo recorrida pre-order6.png]]

---
![[Ejemplo recorrida pre-order7.png]]

---
![[Ejemplo recorrida pre-order8.png]]

---
![[Ejemplo recorrida pre-order9.png]]

---
![[Ejemplo recorrida pre-order10.png]]

---
![[Ejemplo recorrida pre-order11.png]]

---
![[Ejemplo recorrida pre-order12.png]]

---
##### Ejemplo, recorrida in-order
![[Ejemplo recorrida in-order.png]]

---
##### Ejemplo, recorrida pos-order
![[ejemplo recorrida post-order.png]]

---
### Otras 3 maneras de recorrer árboles binarios
Hay otras tres maneras de recorrer: en cada una de las anteriores, intercambiar el orden entre las recorridas de los subárboles. Por ejemplo:
```haskell
in_order_der_izqA Vacío = [ ] 
in_order_der_izqA (Nodo i r d) = in_order_der_izqA d ++ (r : in_order_der_izqA i)
```
---
### Otra manera más de recorrer árboles binarios
![[otra manera de recorrer.png]]
Observaciones:
- salvo la última, todas las formas anteriores de recorrer, primero recorren ***en profundidad***
- la última que presentamos, no recorre ***a lo ancho***.
- Todas las otras son ejemplo de ***DFS*** (Depth-first search).
- La última es ejemplo de ***BFS*** (Breadth-first search).
- Un programa que recorra en BFS es más difícil de escribir.