merge_sort = Ordenación por intercalación

## En el lenguaje imperativo
*{Pre: n ≥ rgt ≥ lft > 0 ∧ a = A}*
```LenguajeImperativo
proc merge_sort_rec(in/out a: array[1..n] of T, in lft, rgt: nat)
	var mid: nat
	if (rgt > lft) --->
		mid:= (rgt + lft) / 2
		merge_sort_rec(a, lft, mid)
		{- a[lft, mid] permutación ordenada de A[lft, mid] -}
		merge_sort_rec(a, mid + 1, rgt)
		{- a[mid + 1, rgt] permutación ordenada de A[mid + 1, rgt] -}
		merge(a, lft, mid, rgt)
		{- a[lft, rgt] permutación ordenada de A[lft, rgt] -}
	fi
end proc
```
*{Post: "a" permutación de A ∧ a[lft, rgt] permutación ordenada de A[lft, rgt]}*

##### Funciones auxiliares:
- [[merge]]

### Algoritmo principal
```LenguajeDeLaMAteria
proc merge_sort(in/out a: array[1..n] of T)
	merge_sort_rec(a, 1, n)
end proc
```

### Ejemplo:
![[Ejemplo de merge_sort.png]]