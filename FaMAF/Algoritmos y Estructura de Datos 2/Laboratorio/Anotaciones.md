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
`exit` esta incluida en la librería `<stdlib.h`. Esta especificada como:
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
