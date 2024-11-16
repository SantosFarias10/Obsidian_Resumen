### Buffer de Datos
- Imaginemos cualquier situación en que ciertos datos deben transferirse desde una unidad a otra,
- por ejemplo, datos (¿Una foto? xd) que se quiere subir a algún sitio de Internet desde un disco,
- un agente suministra o produce datos (el disco) y otro que los utiliza o consume (el sitio de Internet),
- esta relación se llama ***Productor-Consumidor***
- para amortiguar el impacto por la diferencia de velocidades, se puede introducir un ***buffer*** entre ellos,
- un ***buffer*** recibe y almacena los datos a medida que se producen y los emite en el mismo orden, a medida que son solicitados.
---
### Gráficamente
![[buffer.png]]

---
### Interés
- El programa que realiza la subida de datos puede liberar más rápidamente la lectora del disco.
- El proceso que realizaba la lectura se desocupa antes.
- El producto se ocupa de lo suyo.
- El consumidor se ocupa de lo suyo.
- El buffer se ocupa de la interacción entre ambos.
---
### Uso del Buffer
- La interposición del buffer no debe afectar el orden en que los datos llegan al consumidor.
- El propósito es sólo permitir que el producto y el consumidor puedan funcionar cada uno a su velocidad sin necesidad de sincronización.
- El buffer inicialmente está ***Vacío***.
- A medida que se van ***Agregando*** datos suministrados por el productor, los mismo van siendo alojados en el buffer.
- Siempre que sea necesario enviar un dato al consumidor, habrá que comprobar que el buffer no se encuentre ***Vacío*** en cuyo caso se enviará el ***Primero*** que llegó al buffer y se lo ***Eliminará*** del mismo.
---
### Cola
- Es ***Algo***, con lo que se pueda:
	- Inicializar ***Vacía***.
	- Agregar o ***Encolar*** un dato.
	- Comprobar si quedan datos en el buffer, o sea, si ***Es*** o no ***Vacía***.
	- Quitar o ***Decolar*** un dato.
- El primer dato que se agregó, es el primero que debe enviarse y quitarse de la ***Cola***.
- Por eso se llama ***Cola*** o también ***Cola FIFO*** (First-In, First-Out) (FIFO se ve en sistemas operativos xd).
---
### TAD Cola
- La cola se define por lo que sabemos: Sus 5 operaciones
	1) Inicializar en ***Vacía***.
	2) ***Encolar*** un nuevo dato (o elemento).
	3) comprobar si ***Está Vacía***.
	4) Examinar el ***Primer*** elemento (si no está Vacía).
	5) ***Decolarlo*** (si no está Vacía)
- Las operaciones ***Vacía*** y ***Encolar*** son capaces de generar todas las colas posibles,
- ***Está Vacía*** y ***Primero***, en cambio, solamente examina la cola,
- ***Decolarla*** no genera más valores que los obtenibles por ***Vacía*** y ***Apilar***.
---
### Especificación del TAD Cola
```LenguajeDeLaMateria
spec Queue of T where
```

***Constructors***
```LenguajeDeLaMateria
fun empty_queue() ret q: Queue of T
{- Crea una cola Vacía -}

proc enqueue (in/out q: Queue of T, in e: T)
{- Agrega el elemento 'e' al final de la cola 'q' -}
```

***Operations***
```LenguajeDeLaMateria
fun is_empty_queue(q: Queue of T) ret b: bool
{- Devuelve True si la cola es Vacía -}

fun first(q: Queue of T) ret e: T
{- devuelve el elemento que se encuentra al comienzo de 'q' -}
{- PRE: not is_empty_queue(q) -}

proc dequeue(in/out q: Queue of T)
{- Elimina el elemento que se encuentra al comienzo de 'q' -}
{- PRE: not is_empty_queue(q) -}
```
---
### Algoritmo de transferencia de datos con buffer
```LenguajeDeLaMateria
proc buffer()
	var d: data
	var q: queue of data
	empty_queue(q)
	do (not finish()) --->
		if (there_is_product()) then
			  d:= get_product()
			  enqueue(q, d)
		   (there_is_demand() ∧ ¬is_empty(q)) then
			  d:= first(q)
			  consume(d)
		fi
	od
end proc
```
- Asumimos que hay varias funciones definidas
- `finish()`. Devuelve true cuando el servicio de productor-consumidor haya finalizado.
- `there_is_product()` y `there_is_demand()` devuelven si hay producción o demanda respectivamente.
- `get_product()` obtiene un producto desde el producto, y `consume(d)` le envía al consumidor el producto 'd'.
---
### Utilización
- El TAD cola tiene numerosas aplicaciones.
- Siempre que se quieran atender pedidos, datos, etc. en el orden de llegada
- Una aplicación interesante es el algoritmo de ordenación llamado [Radix Sort](https://es.wikipedia.org/wiki/Ordenamiento_Radix).