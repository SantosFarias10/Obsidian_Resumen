
*{Pre: $a=A ∧ 1≤i,j≤n$}*
**Proc** swap (**in/out** a: **array**[1..n] **of T**, **in** i,j: **nat**)
	**var** tmp: **T**
	tmp := a[i]
	a[i] := a[j]
	a[j] := tmp
**end proc**
*{Post: a[i] = A[j] ∧ a[j] = A[i] ∧ ∀k ∉ {i,j} => a[k] = A[k]}*
**¡Garantiza Permutación!**
