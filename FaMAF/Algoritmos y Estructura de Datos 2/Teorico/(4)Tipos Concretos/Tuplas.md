## Declaración
También llamados registros (records) o estructuras (structs). Las tuplas representan productos cartesianos de distintos tipos. Se puede definir un nuevo tipo mediante una tupla de la siguiente forma:
```LenguajeDeLaMateria
type tperson = tuple
				name: string
				age: nat
				weight: real
			   end tuple
```
## Uso
- El tipo así definido corresponde al producto ***String*** x ***Nat*** x ***Real***, y name, age y weight se llaman ***Cuerpo***.
- Para acceder a los campos de un elemento de tipo tupla utilizamos el operador de acceso escrito con un punto:
```LenguajeDeLaMateria
	var manu = tperson
	manu.name:= "Emmanuel"
	manu.age:= 33
	manu.weight:= 68
```
- En la mayoría de los lenguaje de programación, cuando se ejecuta un programa los elementos de un tipo tupla son alojados en espacios contiguos de memoria y el espacio que ocupa es igual a la suma de los espacios que ocupan sus campos.