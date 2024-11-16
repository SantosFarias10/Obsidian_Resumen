Quick_sort = Ordenación Rápida.

---
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

---
### Algoritmo Principal
```LenguajeDeLaMateria
proc quick_sort(in/out a: array[1..n] of T)
	quick_sort_rec(a, 1, n)
end proc
```
---
### Procedimiento partition
```LenguajeDeLaMaeria
proc partition(in/out a: array[1..n] of T, in lft, rgt: nat, out ppiv: nat)
	var i, j: nat
	ppiv:= lft
	i:= lft + 1
	j:= rgt
	do (i ≤ j) --->
		if (a[i] ≤ a[ppiv]) --->
			i:= i + 1
		else if (a[j] > a[ppiv]) --->
			j:= j - 1
		else if (a[i] > a[ppiv] ∧ a[j] < a[ppiv]) --->
			swap(a, i, j)
			i:= i + 1
			j:= j + 1
		fi
	od
	swap(a, ppiv, j) {- dejando el pivot en una posición más central -}
	ppiv:= j {- señalando la nueva posición del pivot -}
end proc
```
---
### Invariante
![[Invariante de partition.png]]
- *{Pre: 1 ≤ lft < rgt ≤ n ∧ a = A}*
- *{Post: a[1,lft) = A[1,lft) ∧ a(rgt,n] = A(rgt,n] ∧ a[lft,rgt] permutación de A[lft,rgt] ∧ lft ≤ piv ≤ rgt ∧ los elementos de a[lft,piv] son ≤ que a[piv] ∧ los elementos de a(piv,rgt] son > que a[piv]}*
- {Inv: : lft = piv < i ≤ j+1 ≤ rgt+1 ∧ todos los elementos en a[lft,i) son ≤ que a[piv] ∧ todos los elementos en a(j,rgt] son > que a[piv]
---
### Ejemplo
![[Ejemplo de quick_sort.png]]
---
### Ejemplo de partition
![[Ejemplo de partition.png]]