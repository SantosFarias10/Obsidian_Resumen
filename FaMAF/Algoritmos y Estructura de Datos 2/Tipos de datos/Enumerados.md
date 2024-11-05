## Declaración
- Un tipo enumerado consiste de una serie finita de constantes, que serán los únicos elementos del tipo.
- En nuestro lenguaje el programador puede definir un nuevo ***Tipo Enumerado*** de la siguiente forma:
```LenguajeDeLaMateria
type E = enumerate
		elem1
		elem2
		.....
		elemK
	  end enumerate
```

## Uso
- El tipo E así definido tiene ***Únicamente*** los K elementos
```LenguajeDeLaMateria
elem1, elem2, ..., elemK
```
- Es un programa se puede declarar una variable del tipo enumerado E y asignarla a una de las constantes que define:
```LenguajeDeLaMateria
	var e: E
	e:= elem2
```
- En nuestro lenguaje permitimos realizar ciclos ***For*** donde el índice tome los valores de un tipo enumerado:
```LenguajeDeLaMateria
	for i:= elem1 to elemK do .... od
```

- Cuando definimos un tipo enumerado, sus elementos tendrán el orden en que fueron escritos en la definición. En el ejemplo anterior elem1 < elem2 < ... < elemK
- El tipo concreto ***Char*** también puede verse como un tipo enumerado donde las constantes estarán ordenadas lexicográficamente('a' < 'b' < 'c' < ...).