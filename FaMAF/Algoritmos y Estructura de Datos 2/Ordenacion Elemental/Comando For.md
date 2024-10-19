Fragmentos de la siguiente forma aparecen con frecuencia:
```LenguajeDeLaMateria
k:= n
do k ≤ m --->
	k:= k+1
od
```

por simplicidad, lo reemplazamos por

```
for k:= n to m do C od
```

siempre que **k** no se modifique en **C**.
Ademas, asumiremos que el **for** declara la variable k, cuya vida dura solo durante la ejecución del ciclo.
