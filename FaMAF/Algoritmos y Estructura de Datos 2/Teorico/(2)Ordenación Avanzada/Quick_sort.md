Quick_sort = Ordenación Rápida.

### Implementación de Quick_sort
*{Pre: 0 ≤ rgt ≤ n ∧ 1 ≤ lft ≤ n + 1 ∧ lft - 1 ≤ rgt ∧ a = A}*
```LenguajeDeLaMateria
proc quick_sort_rec(in/out a: array[1..n] of T, in lft, rgt: nat)
	var ppiv: nat
	if (rgt > lft) --->
		partition(a, lft, rgt, ppiv)
		{- lft ≤ ppiv ≤ rgt 
			elemento en a[lft, ppiv - 1] ≤ a[ppiv]
			elentos en a[ppiv + 1, rgt] ≥ a[ppiv] -}
		quick_sort_rec(a, lft, ppiv - 1)
		quick_sort_rec(a, ppiv + 1, rgt)
	fi
end proc
```
*{Post: "a" es permutación de A ∧ a[lft, rgt] permutación ordenada de A[lft, rgt]}*

##### Funciones auxiliares
- [[Partition]]

### Algoritmo Principal
```LenguajeDeLaMateria
proc quick_sort(in/out a: array[1..n] of T)
	quick_sort_rec(a, 1, n)
end proc
```

### Ejemplo
![[Ejemplo de quick_sort.png]]