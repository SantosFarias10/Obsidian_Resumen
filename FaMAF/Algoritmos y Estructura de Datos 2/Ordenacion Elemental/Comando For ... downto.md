Fragmento de la siguiente forma también aparecen con cierta frecuencia:
```LenguajeDeLaMateria
k:= m
do k ≥ n --->
	C
	k:= k-1
od
```

por simplicidad, lo reemplazaremos por 

```LenguajeDeLaMateria
For k:= m downto n do C od 
```

Siempre que **k** no se modifique en **C**