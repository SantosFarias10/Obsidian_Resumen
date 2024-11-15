
### Intercalación en el lenguaje de la materia
```LenguajeDeLaMateria
proc marge(in/out a: array[1..n] of T, in lft, rgt: nat)
	var tmp: array[1..n] of T
	var j, k: nat
	for i:= lft to mid do
		tmp[i]:= a[i]
	od
	j:= lft
	k:= mid + 1
	for i:= lft to rgt do
		if (j ≤ mid) ∧ (k > rgt ∨ tmp[j] ≤ a[k]) then
			a[i]:= tmp[j]
			j:= j + 1
		else
			a[i]:= a[k]
			k:= k + 1
		fi
	od
end proc
```

### Invariante
![[Invariante del Procedimiento intercalación.png]]

### Ejemplo de intercalación
![[Ejemplo de intercalacion.png]]