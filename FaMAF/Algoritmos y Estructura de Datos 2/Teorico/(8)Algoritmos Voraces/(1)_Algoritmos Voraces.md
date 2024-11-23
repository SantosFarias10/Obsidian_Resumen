Algoritmos Voraces (o Glotones, ***Golosos***) (Greedy)
- Es la técnica más sencilla de resolución de problemas.
- Normalmente se trata de algoritmos que resuelven problemas de ***Optimización***, o sea, tenemos un problema que queremos resolver de manera ***Óptima***:
	- El ***Camino más corto*** que une dos ciudades,
	- El ***Valor Máximo*** alcanzable entre ciertos objetos,
	- El ***Costo Mínimo*** para proveer un cierto servicio,
	- El ***Menor Número de Billetes*** para pagar un cierto importe,
	- El ***Menor Tiempo*** necesario para realizar un trabajo, etc.
- Los Algoritmos Voraces intentan construir la solución óptima buscada ***Paso a Paso***,
- ***Eligiendo*** en cada paso la ***Componente*** de la solución que ***Aparece*** más apropiada.
---
### Características
- Nunca revisa una ***Elección*** ya realizada,
- confían en haber elegido bien las componentes anteriores.
- Por ello, lamentablemente, no todos los problemas admiten solución voraz,
- pero varios problemas interesantes sí admiten solución voraz,
- y entonces, dicha solución resultan muy eficientes.
---
# Forma general de los Algoritmos Voraces

### Ingredientes Comunes 
- Se tiene un problema a resolver de manera ***Óptima***,
- un conjunto de ***Candidatos*** a integrar la solución,
- los candidatos se van clasificando en 3:
	1. Los aún ***No Considerados***.
	2. Los ***Incorporados*** a la solución parcial.
	3. Los ***Descartados***.
- Tenemos una manera de saber si los candidatos ya incorporados completan una ***Solución*** del problema (sin preocuparse por si misma es o no óptima),
- una función que comprueba si un candidato o un conjunto de candidatos es ***Factible*** de formar parte de la solución.
- finalmente, otra función que ***Selecciona*** de entre los candidatos aún no considerados, el más promisorio.

### Receta General
- Inicialmente ningún candidato ha sido considerado, o sea, ni incorporado ni descartado.
- En cada paso se utiliza la función de ***Selección*** para elegir cuál candidato considerar.
- Se chequea que el candidato considerado sea ***Factible*** para incorporarlo a la solución y se lo agrega o no.
- Se repiten los pasos anteriores hasta que la colección de candidatos elegidos sea una solución.

### Esquema general
```LenguajeDeLaMateria
fun voraz(C: Set of "Candidatos") ret S: "Solución a construir"
	S:= "Solución Vacía"
	do (S "No es Solución") --->
		c:= "Seleccionar" de C
		elim(C,c)
		if ("Agregar c a S es factible") then
			"Agregar c a S"
		fi
	od
end fun
```
Lo más importante es el ***criterio de selección***

---
