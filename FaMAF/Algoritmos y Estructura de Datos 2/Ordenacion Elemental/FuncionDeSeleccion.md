*{Pre: $0<i≤n$}*
**Fun** min_pos_from(a: **array**[1..n] **of T**, i: **nat**) **ret** minp: **nat**
	**var** j: **nat**
	minp:= i
	j:= i+1          {Inv: a[minp] es el mínimo de a[i,j)}
	**do** $j≤n$ --->
		**if** $a[j] < a[minp]$ **then**
			minp:= j
		**fi**
		j:= j+1
	**od**
**end proc**
*{Post: a[minp] es el mínimo de a[i,n]}*

Con el [[Comando For]]
**Fun** min_pos_from(a: **array**[1..n] **of T**, i: **nat**) **ret** minp: **nat**
	minp:= i
	**for** $j:= i + 1$ **to** n **do** 
		**if** $a[j] < a[minp]$ **then**
			minp:= j
		**fi**
	**od**
**end fun**
