- Lenguaje inventado hace unos años específicamente para la materia.
- Inspirado remotamente en el lenguaje Pascal.
- Definido informalmente en sintaxis y semántica.
- Esfuerzo recientes en formalizarlo e implementarlo.

Matías Federico Gobbi. “Semántica estática para un lenguaje Pascal-like”. Trabajo Especial de Licenciatura en Ciencias de la Computación. 2021.
https://rdu.unc.edu.ar/handle/11086/17366

### Repaso Algoritmos 1
- Teórico 2022-10-4:
	- Vídeo: [Parte 1](https://drive.google.com/file/d/1Zh6cnRYY94kTh3fpJuIV_QflzMzMRq7K/view?usp=sharing), [parte 2](https://drive.google.com/file/d/10kPeUxUASx5LwgrB7doCZtk30eJWk6Q0/view?usp=sharing)
	- [Pizarra](https://docs.google.com/document/d/1mK5scgBPFy79QT24Shpy6MuYxRGujDqdcesKQFrcmRY/edit?usp=sharing).
- Teórico 2020-10-20:
	- [Vídeo](https://drive.google.com/file/d/1vlA-E3WMpB2e46kYCxIdjGiF9BAdwy6A/view?usp=sharing)
	- [Pizarra](https://docs.google.com/document/d/1Na9aBD7AbvRrPbVdFn7zUGx9PmPTU1Oe75qkibQRprI/edit#)

***Sintaxis***: ¿Cómo se escriben los programas? Un programa es un texto (una secuencia de letras). La ***sintaxis*** de un lenguaje me dice qué textos son programas válidos.
***Semántica***: ¿Qué significan? ¿Qué hacen? Un programa se puede ejecutar, y esa ejecución tiene efecto en un “mundo semántico”. En el caso de los programas imperativos, este mundo semántico es el estado.

## Procedimientos
Un procedimiento encapsula un bloque de código con su respectiva declaración de variables (que define el estado).

#### Sintaxis:
```LenguajeDeLaMateria
proc NOMBRE (<in | out | in/out> p1: T1, ..., <in | out | in/out> pn: Tn)
	<declaraciones de variables>
	<sentencias>
end proc
```

donde p1, p2, ..., pn son nombres de variables (son los parámetros), y T1, T2, ..., Tn son sus respectivos tipos.

#### Tipos de Parámetros:
- ***in***: el parámetro es de entrada (no se puede modificar).
- ***out***: el parámetro es de salida.
- ***in/out***: el parámetro es de entrada/salida.

#### Ejemplo de procedimiento
```LenguajeDeLaMateria
proc abs(in x: int, out y: int)
	if x >= 0 then
		y:= x
	else
		y:= -x
	fi
end proc
```
(acá n = 2, p1 es x, p2 es y, T1 es int, T2 es int)

#### Ejemplo de uso de este procedimiento:
```LenguajeDeLaMateria
proc llamadorDeAbs() <--- declaro un proc que tiene 0 parametros
	var a, b: int 
	a:= -10
	abs(a, b)
	{- acá vale que b = 10 -} <-- esto es un comentario 
end proc
```

#### observaciones:
- Los procedimientos no devuelven cosas (pero pueden escribir varios parámetros out)
- Las llamadas a procedimientos son sentencias del lenguaje.

## Funciones
Las funciones son como los procedimientos salvo que todos los parámetros son in, y devuelven algo.

#### Sintaxis
```LenguajeDeLaMateria
fun nombre (p1: T1, p2: T2, ..., pn: Tn) ret r: T
	<declaraciones de variables>
	<sentencias>
end fun
```
donde p1, p2, …, pn y "r" son nombres de variables, y T1, T2, …, Tn, T son sus respectivos tipos.

#### Ejemplo de función:
```LenguajeDeLaMateria
fun abs (x: int) ret y: int
	if x>= 0 then
		y:= x
	else 
		y: = -x
	fi
end fun
```

#### Ejemplo de uso de está función:
```LenguajeDeLaMateria
proc llamadorDeAbs() <--- declaro un proc que tiene 0 parametros
	var a, b: int
	a:= -10
	b:= 35
	b:= abs(a)
	a:= abs(a) + 10
	{- acá vale que b = 10, a = 20 -} <-- esto es un comentario xd
end proc
```

#### Observaciones sobre las funciones:
- No hay sentencia “return”. Se devuelve lo que sea asignado a la variable declarada como “ret”. La variable se puede usar libremente en el cuerpo de la función (se puede leer, asignar varias veces, etc.).
- Las llamadas a funciones ***NO son sentencias***, son ***expresiones*** (e.g. se puede usar en la parte derecha de una asignación, en una guarda, etc).
- Las funciones no tienen efectos colaterales en el estado. Una función no puede escribir las variables correspondientes a los parámetros.

#### Observaciones generales:
- Cada función y procedimiento define un estado propio llamado “contexto”. Las variables declaradas dentro de funciones y procedimientos no existen fuera de éstas.
- Cualquier función o procedimiento pueden llamar a cualquier otra función o procedimiento. Incluso pueden llamarse a sí mismas (recursión), y también mutuamente (recursión mutua).
- No importa el orden en que se declaran, un procedimiento puede llamar a otro que esté definido más adelante.
- ***NO se pueden*** definir procedimientos ni funciones dentro de procedimientos o funciones (no hay anidamiento, todas las definiciones están al mismo nivel).

## Tipos Nativos
Los tipos nativos son los tipos que trae predefinidos el lenguaje de programacion.

#### Tipos básicos
- Bool: booleanos (true y false)
- Int: Números enteros.
- Nat: Números naturales (incluido el 0 como debe ser).
- Real: Números reales:
- Char: caracteres ('a', 'j').
- String: secuencias de caracteres ("pi").

Usaremos constantes que nos sirvan como infinito, -infinito, etc.

#### Tipos estructurados:
- Array: arreglos.
- Pointer: punteros (se ve en la materia xd).

## Arreglos

#### Sintaxis de Arreglos:
- Declaraciones de una variable de tipo arreglo:

```LenguajeDeLaMateria
var a: array[N1 .. M1, ... , Nk .. Mk] of T
```

- donde:
	- a: es el nombre de la variable.
	- k: es la cantidad de dimensiones del arreglo.
	- N1,M1, ..., Nk,Mk: son números que indican el rango del arreglo para cada dimensión.
	- T: es el tipo de los elementos del arreglo.

- Acceso (es una expresión):

```LenguajeDeLaMateria
a[i, ... , ik]
```

- Asignación (es una sentencia):

```LenguajeDeLaMateria
a[i1, ... , ik] := E
```

donde E es una expresión de tipo T.

#### Ejemplos
```LenguajeDeLaMateria
var precios: array[1..10] of int
{- arreglo de 10 elementos precios[1], precios[2], ..., precios[10] -}

var matriz: array[0..25, 5..10] of char
	{- arreglo de caracteres de dos dimenciones (26 x 6): 
	matriz[0,5], matriz[0,6], matriz[0,7], ..., matriz[0,10]
	matriz[1,5], matriz[1,6], matriz[1,7], ..., matriz[1,10]
	...
	matriz[25,5], matriz[25,6], matriz[25,7], ..., matriz[25,10]
	-}
```

## Definiciones de Tipos Nuevos
En la materia se ve:
- Sinónimos de tipo.
- Tipos enumerados.
- Tuplas.

## Expresiones
Básicamente las mismas que usamos en Algoritmos 1, agregando la posibilidad de llamar a funciones.

#### Expresiones validas en programación imperativas:
- Valores constantes.
- Variables y constantes declaradas.
- Operaciones básicas (+, -, *, /, div, mod, max, min, ∧, ∨, ¬, =, ≠, ≤, <, >, etc).
- Acceso a elementos de arreglos.
- Llamadas a funciones.

## Sentencias

### Skip
La sentencia que no hace nada xd.

***Sintaxis***: skip.

### Asignación (:=)
***Sintaxis***: 
```LenguajeDeLaMateria
v:= E
```
Donde "v" es una variable y E es una expresión.

##### Observación:
- No tenemos más la asignación múltiple.

### Llamada a procedimiento
***Sintaxis***:
```LenguajeDeLaMateria
nombreProc(e1, ..., en)
```

***Semántica***: Ya la vimos

### Condicional (if)
***Sintaxis***: 
```LenguajeDeLaMateria
if B then
	s1
else
	s2
fi
```

***Sintaxis***:
```LenguajeDeLaMateria
if B1 -> s1
	B2 -> s2
	...
fi
```
o también sin el else
```LenguajeDeLaMateria
if B then
	s1
fi
```
donde B es una expresión booleana, y s1, s2 son sentencias.

***Semántica***: La usual. El if es ***determinista***.

### Repetición (while)
***Sintaxis***:
```LenguajeDeLaMateria
while B do
	S
od
```
Donde B es una expresión booleana y S es una sentencia.

***Semántica***: La usual.

## Otra repetición ("for to" y "for downto")
***Sintaxis  del "for to"***:
```LenguajeDeLaMateria
for i:= N to M do
	S
od
```
donde N y M son expresiones de tipo int y S es sentencia.

***Semántica***: 
1. Se declara la variable "i" (sólo existirá dentro de la sentencia S)
2. Se la asigna a "i" el valor N
3. Se ejecuta S
4. Se incrementa "i" en 1
5. Si "i" > M termina, si no vuelve al punto 3.
6. "i" deja de existir al termina.

***Sintaxis del "for downto"***:
```LenguajeDeLaMateria
for i:= N downto M do
	S
od
```
donde N y M son expresiones de tipo int y S es sentencia.

***Semántica***: Igual que el "for to" pero restando 1.

##### Observaciones:
- NO hace falta declarar "i" (el for mismo ya la declara).
- Si había otra variable "i" afuera, esta "i" la tapa.
- ***No se puede modificar*** "i" con asignaciones en el cuerpo del ciclo (S).
- No agrega expresividad al lenguaje (todo se puede hacer con while).
- Itera desde N hasta M ***inclusive***

##### Ejemplos: Declaramos un arreglo y lo llenamos de ceros de izquierda a derecha
```LenguajeDeLaMateria
var precios: array[1..100] of int
for i:= 1 to 100 do
	precios[i]:= 0
od
```

##### Ejemplo sin arreglos: El factorial de un numero n
```LenguajeDeLaMateria
var n, fac: int
n:= 100
fac:= 1
for i:= 1 to n do
	fac:= fac * i
od
```

##### Ejemplo con el downto: recorro un arreglo de derecha a izquierda
```LenguajeDeLaMateria
var precios: array[1..100] of int
precios[100] := 35
for i:= 99 downto 1 do
	precios[i]:= precios[i+1] * 2
od
{- pregunta: ¿Cuanto vale precios[98]? -}
```