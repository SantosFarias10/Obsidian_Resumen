- Son casos particulares de árboles binarios,
- son árboles binarios " t " en donde la información está organizada de forma tal que el siguiente algoritmo sencillo permite buscar eficientemente un elemento:
- El elemento buscado se compara con la raíz de " t "
	- Si es el ***mínimo***, la búsqueda finaliza.
	- Si es ***menor que la raíz***, la búsqueda se restringe al subárbol izquierdo de " t " con el mismo algoritmo.
	- Si es ***mayor que la raíz***, la búsqueda se restringe al subárbol derecho de " t " con el mismo algoritmo.
- Si el árbol está "balanceado", es un algoritmo logarítmico.
---
### Definición intuitiva
Para que este algoritmo funcione, " t " debe cumplir lo siguiente:
1) Los valores alojados en el subárbol izquierdo de " t " deben ser menores que el alojado en la raíz de " t ".
2) Los valores alojados en el subárbol derecho de " t " deben ser mayores que el alojado en la raíz de " t ".
3) Las condiciones (1) y (2) deben darse para todos los subárboles de " t ".
Si se cumplen estas 3 condiciones, decimos que " t " es un ***árbol binario de búsqueda*** o ***ABB***.
--- 
### Agregar un elemento en un ABB
Si quiero agregar un elemento e a un Árbol Binario de Búsqueda t de manera de mantener la propiedad debo realizar el siguiente procedimiento recursivo:
1) Si ***" t " es vacío***, formo el nodo que consta del elemento " e " y los dos subárboles vacíos.
2) En ***caso contrario***, comparo el elemento " e " con la raíz del árbol " t ",
3) Si ***" e " es menor que la raíz***, lo agrego al subárbol izquierdo.
4) Si ***" e " es mayor o igual a la raíz***, lo agrego al subárbol derecho.
---
### Ejemplo
![[Obsidian_Resumen/FaMAF/Algoritmos y Estructura de Datos 2/Teorico/(7)Árboles/Ejemplo.png]]
***¿¿Es un árbol binario de búsqueda?*** NO
![[Ejemplo_malo.png]]
ya que:
- 60 debe cambiar por uno entre 63 y 68
- 72 debe cambiar por uno entre 77 y 80
- 73 debe cambiar por 70
- 74 debe cambiar por uno entre 77 y 80.
- 80 debe cambiar por uno entre 82 y 88.
Como se muestra a continuación ahre
![[Ejemplo_bueno.png]]