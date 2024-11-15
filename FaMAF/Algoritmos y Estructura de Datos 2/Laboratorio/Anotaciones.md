gcc -Wall -Werror -Wextra -pedantic -std=c99 -o *nombre_para_ejecutar* nombre_del_archivo.c

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

