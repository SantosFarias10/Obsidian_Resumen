```LenguajeDeLaMaeria
proc partition(in/out a: array[1..n] of T, in lft, rgt: nat, out ppiv: nat)
	var i, j: nat
	ppiv:= lft
	i:= lft + 1
	j:= rgt
	do i ≤ j --->
		if a[i] ≤ a[ppiv] --->
			i:= i + 1
		else if a[j] > a[ppiv] --->
			j:= j - 1
		else if a[i] > a[ppiv] ∧ a[j] < a[ppiv] --->
			swap(a, i, j)
			i:= i + 1
			j:= j + 1
		fi
	od
	swap(a, ppiv, j) {- dejando el pivot en una posición más central -}
	ppiv:= j {- señalando la nueva posición del pivot -}
end proc
```

##### Funciones auxiliares
- [swap](swap)

### Invariante
![[Invariante de partition.png]]
- *{Pre: 1 ≤ lft < rgt ≤ n ∧ a = A}*
- *{Post: a[1,lft) = A[1,lft) ∧ a(rgt,n] = A(rgt,n] ∧ a[lft,rgt] permutación de A[lft,rgt] ∧ lft ≤ piv ≤ rgt ∧ los elementos de a[lft,piv] son ≤ que a[piv] ∧ los elementos de a(piv,rgt] son > que a[piv]}*
- {Inv: : lft = piv < i ≤ j+1 ≤ rgt+1 ∧ todos los elementos en a[lft,i) son ≤ que a[piv] ∧ todos los elementos en a(j,rgt] son > que a[piv]}

### Ejemplo
![[Ejemplo de partition.png]]