### Tipos Abstractos
- Separación entre ***Especificación*** e ***Implementación***.
- Se definen especificados constructores y operaciones.
- Podemos tener varias implementaciones para un mismo tipo.
- En general surgen de analizar un problema a resolver.
- El problema evidencia qué necesitamos representar y qué operaciones tener.

### TAD Contador
- Problema:
	- Dar un algoritmo que tome una expresión,
	- dada, por ejemplo, por un arreglo de caracteres,
	- y devuelva verdadero si la expresión tiene sus paréntesis correctamente balanceados,
	- y falso en caso contrario.

Solución conocida
- Recorrer el arreglo de izquierda a derecha y ***contar*** paréntesis que abren y cierran.
- Necesitamos ***Iniciar*** el contador,
- incrementando cada vez que se encuentra un paréntesis que abre,
- decrementarlo (comprobando previamente que no sea nulo cuyo caso ***No están balanceados***) cada vez que se encuentra un paréntesis que cierra,
- Al finalizar, comprobar que la cuenta esté en el estado inicial.
---
- El contador se define por lo que sabemos de momento: sus 4 operaciones
	- inicializar.
	- incrementar.
	- comprobar si su valor es inicial.
	- decrementar si no lo es.
- Notamos que las operaciones ***Inicializar*** e ***Incrementar*** son capaces de generar todos los valores posibles del contador, por lo que serán nuestros ***Constructores***.
- ***Comprobar*** en cambio solamente examina el contador,
- ***Decrementar*** no genera más valores que los obtenibles por ***Inicializar*** e ***Incrementar***.