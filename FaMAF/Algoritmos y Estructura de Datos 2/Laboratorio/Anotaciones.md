- gcc -Wall -Werror -Wextra -pedantic -std=c99 -o *nombre_para_ejecutar* nombre_del_archivo.c
- gcc -Wall -Werror -Wextra -pedantic -std=c99 -c nombres_de_los_archivos.c
- gcc -Wall -Werror -Wextra -pedantic -std=c99 nombres_de_los_objetos.o -o nombre_para_ejecutar

Mis labs: https://github.com/SantosFarias10/Repaso_Lab_Algoritmos2

---
# LAB1 

### fopen()
`fopen` esta especificada de la siguiente forma: `FILE *fopen(const char *filename, const char *mode);`. Esta incluida en la librería `<stdio.h>` y lo que hace es abre el archivo especificado por ***filename***. El parámetro ***mode*** es una serie de caracteres que especifica el tipo de acceso que se solicita para el archivo (o sea si nosotros queremos leer el archivo le pasamos un carácter solamente para leer (r), o para escribir () o para leer y escribir (r+), etc). Una vez completada la función `fopen` devuelve el puntero a FILE, en caso contrario de vuelve `null` en caso de error. Ejemplo: 

`FILE *file = fopen(filepath, "r");`
Definimos el file y con la función `fopen` le damos como argumento el filepath (dirección del archivo) y como segundo argumento le pedimos que solamente lea.

### fclose()
`fclose` esta incluido en la librería `<stdio.h>`. Esta especificado como:
`int fclose(FILE *stream);`
La función `fclose()` cierra una ruta a la que apunta ruta. Esta función suprime todos los almacenamientos intermedios que están asociados con la corriente antes de cerrarla. Cuando cierra la corriente, la función libera los almacenamientos intermedios reservados por el sistema. Cuando se cierra una corriente binaria, el último registro del archivo se rellena con caracteres nulos (\0) hasta el final del registro.
retorna 0 si cierra correctamente la ruta o EOF ((End Of File) es una **constante** definida en la biblioteca estándar `<stdio.h>` de C que representa el **fin de un archivo** o un **error** en operaciones de entrada/salida. Su valor es típicamente `-1`.) si se ha detectado algún error.

### fscanf()
`fscanf` esta incluida en la librería `<stdio.h>`. Esta especificada como: 
`int fscanf (FILE *stream, const char *format-string, argument-list);`
`fscanf` lee los datos de la posición actual de la ruta especificada en las ubicaciones que proporcionan las entradas de la lista-argumentos, si las hay. Cada entrada de lista-argumentos debe ser un puntero a una variable con un tipo que corresponda a un especificador de tipo en serie-formato. Retorna el número de campos que ha convertido y asignado correctamente. El valor de retorno no incluye los campos que la función `fscanf()` ha leído pero no ha asignado. Ejemplo

`fscanf(file, "%u", &size);`
con la función `fscanf` escaneamos file, especificamos el formato para leer números sin signo (unsigned int) (%u) y guardándolo en la variable size.

### fprintf()
`fprintf` esta incluida en la librería `<stdio.h>`. Esta especificada como:
`int fprintf(FILE *stream, const char *format-string, argument-list);`
La función `fprintf()` formatea y escribe una serie de caracteres y valores en la salida ruta, convierte cada entrada en lista-argumentos, si la hay, y escribe en la ruta de acuerdo con la especificación de formato correspondiente en la serie-formato.
Retorna el número de bytes que se imprimen o un valor negativo si se produce un error de salida.

### exit()
`exit` esta incluida en la librería `<stdlib.h>`. Esta especificada como:
`void exit(int status);`
La función `exit()` permite finalizar la ejecución de un programa de manera controlada, devuelve el control al entorno de sistema principal desde el programa. 
El argumento `status` indica el **código de salida** del programa, puede tener un valor de 0 a 255 inclusive, o puede ser una de las macros `EXIT_SUCCESS` (Es una constante que representa un valor estándar (generalmente 0) para indicar que el programa terminó con éxito) o `EXIT_FAILURE` (Es una constante que representa un valor estándar (generalmente distinto de 0, como 1) para indicar que el programa terminó con un error).

### Cositas
Recordar que ***typedef*** define sinónimos de tipos (como type en Haskell)
Por ejemplo:  `mybool` es un sinónimo de `int` (son el mismo tipo). Además se  
definen las constantes true y false:  
```c
define true 1  
define false 0  

typedef int mybool;
```

### ifndef, define y endif
Las directivas de preprocesador `#ifndef`, `#define` y `#endif` se utilizan para prevenir la inclusión múltiple de un archivo de encabezado en un programa en C. Esto se conoce como "include guard" o "guardas de inclusión".
- **`#ifndef`**: Verifica si una macro no ha sido definida previamente.
- **`#define`**: Define la macro para indicar que el archivo ha sido incluido.
- **`#endif`**: Finaliza la condición iniciada por `#ifndef`.
Estas directivas aseguran que el contenido del archivo de encabezado solo se incluya una vez durante la compilación, evitando problemas de redefinición y errores de compilación.
---
# LAB2

### Cadenas
En C se piensan como arreglos de caracteres. Los ***Caracteres*** son valores del tipo `char` (que representan exactamente un carácter de ***1 byte***), entonces para guardar un `string` en C se puede usar el siguiente arreglo:
```c
char cadena[5];
```
El arreglo `cadena` tiene capacidad para guardar un `string` de hasta 4 caracteres de longitud. Esto es así porque toda cadena en C ***Debe terminar con el carácter ' \0 '*** por lo cual ya tenemos un lugar ocupado. Esta convención permite saber dónde termina la cadena independientemente de la capacidad del arreglo. Entonces, se puede almacenar en ***cadena*** una palabra con longitud de entre uno y cuatro caracteres, pero incluso también se puede guardar una palabra vacía (en ese caso `cadena[0] = '\0'`). Si queremos armar el `string` con la palabra "hola" podemos hacer:
```c
char cadena[5] = {'h', 'o', 'l', 'a', '\0'};
printf("cadena: %s\n", cadena);
```
Importante de no olvidarse de poner el `'\0'` final, ya que si no `printf()` va a recorrer a `cadena[]` por fuera de su rango hasta que aparezca un `'\0'` y al acceder a memoria inválida se producirá una ***Violación de segmento*** (`segmentation fault`). Notar que no hay ningún problema en hacer:
```c
char cadena[10] = {'h', 'o', 'l', 'a', '\0'}
printf("cadena: %s\n", cadena);
```

Otra ***forma más cómoda de armar el `string`*** con la palabra "hola" es hacer algo como:
```c
char cadena[10] = "hola";  
printf("cadena: %s\n", cadena);
```
En este caso el carácter `'\0'` se agrega implícitamente en el arreglo `cadena`. ***Para no tener que contar la cantidad de caracteres*** que necesitamos se puede definir una cadena directamente haciendo:
```c
char cadena[] = "hola mundo!";  
printf("cadena: %s\n", cadena);
```
el contenido del array es:
![[array.png]]
todas estas maneras de armar `strings` solo son válidas al momento de ***inicialización*** del  
arreglo. Si hacemos:
```c
char cadena[] = "hola mundo!";
cadena = "chau mundo!";
printf("cadena: %s\n", cadena);
```
El resultado va a ser un ***error de compilación***. 

### Typedef
Lo que hace typedef es dar un nombre nuevo a un tipo que ya existe en C. Entonces por ejemplo si queremos definir el tipo natural para los números positivos:
```c
typedef unsigned int natural;
```
de esta manera cuando declaremos una variable del tipo natural la variable va a ser equivalente a una variable del tipo unsigned int.
La sintaxis entonces es:
```c
typedef <tipo-existente> <nuevo-tipo>;
```

### break
El mandato `break` permite terminar y salir de un bucle (es decir, `do`, `for` y `while`).

---
# LAB3

### feof()
Indicador de fin de archivo de prueba. Esta incluido en `<stdio.h>` y esta especificado como
`int feof(FILE *stream);`
La función `feof()` indica si se ha establecido el distintivo de fin de archivo para la ruta de proporcionada. El distintivo de fin de archivo lo establecen varias funciones para indicar el final del archivo. 
Retorna un valor distinto de cero si y sólo si se ha establecido el distintivo EOF ; de lo contrario, devuelve 0.

### ferror()
Prueba de errores de lectura/escritura. Esta incluido en `<stdio.h>` y esta especificado como`int ferror(FILE *stream);`.
La función `ferror()` comprueba si se ha producido un error al leer o escribir en la ruta proporcionada. Si se produce un error, el indicador de error para la ruta permanece establecido hasta que cierre ruta.
Retorna un valor distinto de cero para indicar un error en la ruta especificada. Un valor de retorno de 0 significa que no se ha producido ningún error.

---
# Lab4
Un ***puntero*** es un tipo de variable especial que guarda una dirección de memoria. En C se denotan los punteros usando el símbolo * . Es decir que una variable ' p ' declarada como `int *p;` es del tipo ***puntero a int***.
Para el manejo de punteros contamos con dos operadores unarios básicos, el operador de  
referenciación y el de desreferenciación.

#### Operación de referenciación (`&`)
Este operador obtiene la ***dirección de memoria*** de una variable. También se lo conoce como operador de dirección (address operator). 
Si se tiene una variable entera ' x ' declarada como `int x=3;` entonces la expresión `&x` ***retornará la dirección de memoria*** donde está alojado el contenido de la variable ' x '.
![[memoria.png]]
Particularmente la expresión `&x` en este caso es del tipo `int*`, es decir del tipo puntero a int.
se puede hacer lo siguiente:
```c
int x = 3;
int *p;
p = &x;
```
notar que la asignación es correcta porque `p` y `&x` tienen el mismo tipo. En este ejemplo entonces el puntero `p` guarda la ***dirección de memoria*** de `x` y podemos decir que ***p apunta a x***.
![[p apunta a x.png]]

#### Operación de desreferenciación (`*`)
Obtiene el ***valor de lo apuntado por el puntero***. También se lo conoce como el operador de indirección (indirection operator). Se lo puede pensar como una operación de inspección ya que accede al ***valor alojado en una dirección de memoria***. Si se tiene una variable de tipo `int*` llamada `p`, entonces la expresión `*p` retornará el ***valor entero que se aloja en la dirección de memoria que contiene p***. En el ejemplo anterior `*p` devuelve 3, ya que `x = 3`.
Además si se utiliza `*p` del lado izquierdo de una asignación:
```c
*p = <expresión>;
```
la asignación escribirá el resultado de la expresión en la dirección de memoria apuntada por `p`, por lo que se cambia el valor contenido en esa dirección de memoria (sin modificar a `p`, que seguirá apuntando al mismo lugar). 
Por ejemplo, si se hace la asignación:
```c
int x = 3;
int *p;
p = &x;
*p = 10;
```
entonces sucede que
![[cambiando el valor de x.png]]
Notar que se cambió el valor de la variable `x` de manera indirecta usando el puntero `p`.
Cabe aclarar que cuando se declara la variable de tipo puntero `int *p;` el símbolo `*` no actúa como operador sino que simplemente indica que la variable `p` se declara como puntero.
¿Qué valor tendrá la variable `y` luego de ejecutar el siguiente código?
```c
int x = 3;  
int y = 10;  
y = *(&x);    /* y ahora es 3 */
```

#### Constante nula de punteros (`NULL`)
Siempre es buena idea dar un valor inicial a las variables apenas se declaran. Para punteros existe en C la constante `NULL` que representa una ***dirección de memoria nula***, en la cual no se puede leer ni escribir. Esta constante es una macro definida en los ***headers*** de `stdlib.h` como la dirección de memoria 0.
Recordar que ***si no se inicializa una variable esta puede contener cualquier valor***, en el caso de enteros podría ser un número muy grande y extraño (o quizás un inofensivo 4) y en el caso de punteros puede tener asignada una dirección de memoria que en caso de querer escribir o leer de ella generaría problemas. 
Por ejemplo en el siguiente programa:
```c
int *p;  
*p = 3;
```
es fácil imaginar que esto podría generar que el programa termine con un error (violación de segmento - `segmentation fault`) pero podría ser peor. Puede suceder que por azar en `p` se encuentre la dirección de memoria de otra variable del programa y la modifiquemos, generando un ***BUG*** muy difícil de rastrear.
En esta otra versión:
``` c
int *p = NULL;  
*p = 3;
```
el programa siempre va a fallar, y eso es bueno.

### Preliminares: Punteros++
Se vio que un puntero es un tipo de variable especial que guarda una dirección de memoria. La memoria se puede pensar como un gran arreglo, y en ese sentido una dirección de memoria es un índice. A estos índices los escribiremos en base hexadecimal (0, 1, 2, ..., 9, A, B, C, ..., F) que es la base utilizada normalmente para referirse a direcciones de memoria. Volvamos a los ejemplos básicos:
```c
int x = 3;  
int *p;  
p = &x;
```
antes vimos el resultado de este programa con el siguiente esquema:
![[los punteros tambien son variables.png]]
esto, aunque es bastante didáctico, pareciera que deja a `p` por fuera de la memoria. El puntero `p` también es una variable y por lo tanto también tiene su lugar en la memoria. Veamos cómo sería la ejecución del programa en sus distintas etapas (en este caso inicializaremos en `NULL` al puntero):
```c
int x = 3;  
int *p = NULL;
```
![[inicializando en null.png]]
notar que ahora en el esquema se ve que `p` tiene su lugar en la memoria e inicialmente vale `0x00` (el prefijo `0x` indica que es un número hexadecimal) lo cual significa que `p` apunta a `NULL`. Sigamos con el programa:
```c
p = &x;
```
![[p apunta a la direccion de x.png]]
la expresión `&x` hace referencia a la dirección de memoria `0x05`, entonces luego de la asignación, el puntero `p` apunta a la dirección de memoria `0x05`, es decir que `p` simplemente tiene ese valor asignado. Notar que `p`, al ser una variable, su valor también está en algún lugar de la memoria (en la dirección `0x09` en este caso). 
Veamos ahora un ejemplo más complejo:
```c
int x = 3, y = 22;  
int *p = NULL;  
p = &x;  
y = *p;  
*p = 7;
```
nuevamente lo ejecutamos por etapas:
```c
int x = 3, y = 22;  
int *p = NULL;
```
![[nuevo ejemplo.png]]
Ahora se muestra como cambia la memoria para las siguientes tres asignaciones:
```c
p = &x;
```
![[P apunta a la direccion de memoria de x.png]]
```c
y = *p;
```
![[y toma el valor que apunta p.png]]
```c
*p = 7;
```
![[cambiando el valor de x mediante un puntero p.png]]
la primera asignación de `p` hace que apunte a la dirección de `x` que es `0x05`, luego a la variable `y` se le asigna el contenido que hay en la dirección de memoria `0x05` que es (en ese momento) el valor `3`, luego se cambia el contenido de la memoria en la dirección `0x05` y se escribe el valor `7`.
¿Cuál sería el resultado de la expresión `&p`? ¿`0x10`? creo

#### Visualizando direcciones de memoria
Los valores de las variables del tipo puntero (las direcciones de memoria) se pueden visualizar. Por lo general esto no se hace, salvo a veces para hacer ***debug***. La manera es usando `printf()` con `%p`:
```c
int *p = NULL;  
int a = 55;  
p = &a;  
printf("La dirección de memoria apuntada por p es: %p", (void *) p); 
/* la de a */
```
Notar el ***casteo*** que se le realiza a `p` cuando se lo pasa como parámetro a `printf()`. Cuando antes de una expresión se introduce un tipo entre paréntesis, significa que la expresión se va a convertir al tipo indicado. Por ejemplo `(float) 2` hace que el resultado se interprete como `2.0f`, otro ejemplo puede ser `(int) 1.5` que hace que el resultado sea el entero `1`. En este caso se convierte a `p`, que es un `int*`, a un `void*` o sea un puntero a `void` lo cual es simplemente una dirección de memoria pura, puesto que un puntero a `void` no se puede ***desreferenciar***.
La salida del programa va a ser un número en hexadecimal (con prefijo `0x...`), por ejemplo:
```bash
La dirección de memoria apuntada por p es: 0x7ffd15a1ac60
```
Otra forma con la cual se puede ver el valor decimal de una dirección de memoria es hacer:
```c
int *p = NULL;  
int a = 55;  
p = &a;  
printf("El índice de memoria alojado en p es: %lu", (uintptr_t) p);
```
aquí se ***castea*** el puntero a un ***entero sin signo*** que es el tipo `uintptr_t` definido en `<stdint.h>`. Este tipo es lo suficientemente grande para guardar direcciones de memoria, pero la solución no es muy estándar ya que el comodín `"%lu"` podría fallar en algunos sistemas ya que `printf()` espera algo del tipo `long unsigned int` y podría no ser equivalente a `uintptr_t`. La salida sería algo como:
```bash
El índice de memoria alojado en p es: 140724966370400
```

#### Operadores de indexación y flecha
En C además para las variables de tipo puntero se puede usar las operaciones de indexación y el operador flecha (`->`):
- Indexación (`p[n]`): Permite obtener el valor que hay en la memoria moviéndose `n` lugares hacia adelante (de manera alineada al tipo de datos del puntero) desde la dirección de memoria guardada en `p`. Entonces por ejemplo `p[0]` es equivalente a `*p`. Cuando se indexa un puntero se debe tener total seguridad de que se va a acceder a memoria asignada a nuestro programa, de lo contrario ocurrirá un `segmentation fault` (violación de segmento).
- Acceso indirecto (`->`): Si `p` es un puntero a una estructura `p->member` es un atajo a `(*p).member` (asumiendo que la estructura tiene un campo llamado member).

#### Arreglos y punteros
Cuando se declara una variable de tipo arreglo,
```c
int arr[4];
```
hay dos formas de obtener la dirección de memoria al primer elemento:
- Usando el operador de referenciación: `&arr[0]`
- Usando el nombre del arreglo: `arr`
```c
int arr[4] = {1, 9, 8, 6};  
int *p = NULL;
p = &arr[0]; // Usando operador &  
p = arr; // Usando directamente el nombre de variable del arreglo
```
![[apuntando a un array.png]]
¿Qué diferencia hay entre p y arr?
Circunstancialmente se puede usar a `p` para acceder a los elementos del arreglo `arr` ya que `p[i]` y `arr[i]` van a devolver exactamente el mismo valor. Sin embargo, más adelante en el código se puede reutilizar a `p` para que apunte a otra variable, por ejemplo haciendo `p = &x;` (suponiendo que tenemos declarada a `int x;`). Por otro lado, aunque con la expresión `arr` obtenemos la dirección de memoria del primer elemento del arreglo, `arr` no es un puntero ya que no es posible hacer
```c
int arr[4];  
int x;  
arr = &x; // No se puede realizar esta asignación
```

#### Memoria dinámica: Stack vs Heap
En el lenguaje del teórico práctico se usa el procedimiento `alloc()` para reservar memoria para un puntero, y `free()` para liberar dicha memoria:
```LenguajeDeLaMateria
var p: pointer to int  
alloc(p)  
*p := 5  
free(p)
```
En C esto se hace usando las funciones `malloc()` y `free()`:
```c
int *p = NULL;  
p = malloc(sizeof(int));  
*p = 5;  
free(p);
```
La función `malloc()` ***toma un parámetro, que es un entero sin signo de tipo `size_t`*** (muy parecido a `unsigned long int`) que es la ***cantidad de memoria en bytes que se solicita reservar***. A diferencia de `alloc()` del teórico, que automáticamente reserva la cantidad necesaria según el tipo de puntero, ***en C hay que indicar explícitamente la cantidad de bytes a reservar***. El operador `sizeof()` ***devuelve la cantidad de bytes ocupados por una expresión o tipo***, por lo que resulta indispensable para el uso de `malloc()` (aún si uno hubiera memorizado cuantos bytes ocupa cada tipo en su computadora, esto  
puede variar según la versión del sistema operativo o el microprocesador en el que se use el programa).

Las direcciones de memoria que devuelve `malloc()` se encuentran en la sección de memoria denominada ***Heap*** (no confundir con la estructura de datos que lleva el mismo nombre ya que no tiene ninguna relación).
![[como se organiza la memoria.png]]
A modo informativo, el mapa de arriba es un esquema de cómo se organiza la memoria. La sección de ***Código contiene las instrucciones del programa***, la sección ***Global contiene las variables globales***, la sección ***Stack es donde están las variables que usamos en las funciones de nuestro programa (memoria  estática)*** y la sección ***Heap es la región de la memoria dinámica la cual se reserva y libera manualmente mediante `malloc()` y `free()`***. El Stack por su parte se maneja de manera automática, reservando memoria para las variables declaradas en una función que se comienza a ejecutar y liberando esa  
memoria cuando la función termina su ejecución.
Otra gran ***diferencia entre el Stack y el Heap***, es que la cantidad de ***memoria asignada para el Stack es limitada***. ***Si los datos contenidos en el Stack superan dicho límite se genera un stack overflow***. Si durante la ejecución de un programa se está ejecutando una función `f1` y ésta llama a su vez a otra función `f2`, durante la ejecución de `f2` las variables de `f1` siguen en el Stack ya que aún no se terminó de ejecutar. Las variables de las funciones llamadas de manera anidada se van apilando entonces. Hay en consecuencia un límite en la cantidad de llamadas anidadas de funciones, particularmente un número máximo de llamadas recursivas. La cantidad dependerá de cuánta memoria ocupen las variables de las funciones involucradas. Esto hace que si una función declara un arreglo en memoria estática muy grande, podría dejar poco margen para llamadas a otras funciones o  
directamente generar un stack overflow porque el arreglo no entra en el Stack.
Por su parte la ***memoria en el Heap tiene disponible toda la memoria RAM de la computadora***, por lo que mientras haya memoria libre se podrá pedir reservar nueva memoria mediante `malloc()`. Pero un gran poder conlleva una gran responsabilidad, por lo que ***no se debe olvidar liberar la memoria reservada cuando deje de usarse***, puesto que los ***memory leaks*** pueden generar a la larga que la computadora se bloquee por completo.

### malloc()
Esta `#include <stdlib.h>`
Especificación:`void *malloc(size_t size);`
La función `malloc()` reserva un bloque de almacenamiento de tamaño bytes. A diferencia de la función `calloc()` , `malloc()` no inicializa todos los elementos en 0.
Retorna un puntero al espacio reservado. El espacio de almacenamiento al que apunta el valor de retorno está adecuadamente alineado para el almacenamiento de cualquier tipo de objeto. El valor de retorno es `NULL` si no hay suficiente almacenamiento disponible, o si se ha especificado tamaño como cero.
La función `malloc()` ***toma un parámetro, que es un entero sin signo de tipo `size_t`*** (muy parecido a `unsigned long int`) que es la ***cantidad de memoria en bytes que se solicita reservar***. A diferencia de `alloc()` del teórico, que automáticamente reserva la cantidad necesaria según el tipo de puntero, ***en C hay que indicar explícitamente la cantidad de bytes a reservar***. El operador `sizeof()` ***devuelve la cantidad de bytes ocupados por una expresión o tipo***, por lo que resulta indispensable para el uso de `malloc()` (aún si uno hubiera memorizado cuantos bytes ocupa cada tipo en su computadora, esto  
puede variar según la versión del sistema operativo o el microprocesador en el que se use el programa).

### free()
`#include <stdlib.h>`
libera memoria xd, toma como parámetro un puntero, no devuelve ningún valor.

### Preliminares: TADs
Lo primero que debemos observar es la forma en la que logramos mantener separadas la especificación del TAD de su implementación. Cuando definimos un TAD es deseable garantizar encapsulamiento, o sea, que solamente se pueda acceder y/o modificar su estado a través de las operaciones provistas. Esto no siempre es trivial ya que los tipos abstractos están implementados en base a los tipos concretos del lenguaje. Entonces es importante que además de separar la especificación e implementación se garantice que quién utilice el TAD no pueda acceder a la representación interna y operar con los tipos  
concretos de manera descontrolada. Si esto se logra será posible cambiar la implementación del TAD sin tener que modificar ningún otro módulo que lo utilice.

No todos los lenguajes brindan las mismas herramientas para lograr una implementación opaca y se debe usar el mecanismo apropiado según sea el caso. Particularmente el lenguaje del teórico-práctico separa la especificación de un TAD de su implementación utilizando las signaturas `spec ... where` e `implement ... where` respectivamente. En este laboratorio se debe buscar la manera de lograr encapsulamiento usando el lenguaje C.

#### Métodos de TADs
En el diseño de los tipos abstractos de datos (tal como se vio en el teórico-práctico) aparecen los ***constructores***, las ***operaciones*** y los ***destructores***, que se declaran como funciones o procedimientos. Recordar (se vio en el ejercicio 2) que los procedimiento en C no existen como tales sino que se usan funciones con tipo de retorno `void`, es decir, funciones que no devuelven ningún valor al llamarlas. A veces se buscará evitar procedimientos con una variable de salida usando directamente una función para simplificar y evitar así usar punteros extra (en el ejercicio 2 se vio que es necesario usar punteros para simular variables de salida).

A diferencia del práctico, a las precondiciones y postcondiciones de los métodos sí vamos a 
verificarlas (en la medida de lo posible). Recordar que nuestros programas deben ser robustos, por lo tanto cuando corresponda usaremos `assert()` para garantizar el cumplimiento de las pre y postcondiciones de los métodos. Esta práctica es propia de la etapa de desarrollo de un programa, y una vez que el mismo está finalizado, verificado y listo para desplegarlo en producción, se pueden eliminar las aserciones mediante un flag de compilación.
