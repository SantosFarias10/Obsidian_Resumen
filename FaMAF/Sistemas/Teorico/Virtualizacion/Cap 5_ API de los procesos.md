***¡¡¡¡¡Este capitulo sirve mucho para hacer los labs!!!!!!!***

### System Call `fork()`

`fork()` es usada para crear un nuevo proceso

### `p1.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    printf("hello world (pid:%d)\n", (int) getpid());
    int rc = fork();
    if (rc < 0) {
        // fork failed
        fprintf(stderr, "fork failed\n");
        exit(1);
    } else if (rc == 0) {
        // child (new process)
        printf("hello, I am child (pid:%d)\n", (int) getpid());
    } else {
        // parent goes down this path (main)
        printf("hello, I am parent of %d (pid:%d)\n",
        rc, (int) getpid());
    }
    return 0;
}
```

``` bash
prompt> ./p1
hello world (pid:29146)
hello, I am parent of 29147 (pid:29146)
hello, I am child (pid:29147)
prompt>
```

En ***p1.c*** cuando empieza a ejecutarse el proceso imprime un mensaje ***"hello world"***; e incluido en ese mensaje hay un hay un identificador de proceso (***process identifier***), también conocido como ***PID***. El PID es usado para nombrar a los procesos si uno quiere hacer algo con el (Por ejemplo: detenerlo). El proceso tiene un PID de 29146.
El proceso llama a la system call `fork()`, la cual proporciona el SO como forma de crear un nuevo proceso. El proceso creado es (casi) una ***copia exacta del proceso que lo creo***. Esto significa que para el SO ahora hay dos copias del programa ***p1*** ejecutándose, y ambos están por regresar de la system call `fork()`. El proceso recién creado (llamado ***hijo*** (Child)), en contraste del proceso creador ***padre*** (parent) no empieza a ejecutarse en el `main()` (notar que "hello world" solo se imprime 1 vez).
El hijo no es una copia exacta, aunque ahora tiene su propia copia de espacio de direcciones (o sea, su propia memoria privada), sus propios registros, su propio PC, etc, el valor que retorna al creador de `fork()`es diferente. Mientras el padre recibe el PID del hijo, el hijo recibe código de retorno de cero. Esta diferenciación es útil, porque hace simple escribir el código para manejar los dos casos diferentes.
La salida de p1.c no es determinista. Cuando el proceso hijo es creado, ahora hay dos procesos activos en el sistema de los que preocuparnos: el padre y el hijo. Por ejemplo: Asumamos que estamos ejecutando el programa en un sistema con un solo CPU, entonces, el padre o el hijo podrían ejecutarse en ese punto. En el ejemplo de arriba se ejecuto el padre y por lo tanto imprimió el mensaje primero. En otros casos, podría haber ocurrido lo opuesto:
``` bash
prompt> ./p1
hello world (pid:29146)
hello, I am child (pid:29147)
hello, I am parent of 29147 (pid:29146)
prompt>
```
El ***planificador*** del CPU (de ahora en mas CPU `scheduler`) es el que determina que proceso se ejecutara en un tiempo determinado; como el scheduler es complejo, usualmente no podremos asumir que decisión tomara, y que proceso se ejecutara primero.

### System Call `wait()`

A veces, resulta mas útil que el padre espere a que el proceso hijo finalice lo que estaba haciendo. Esta tarea se logra con la system call `wait()` 

### `p2.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>

int main(int argc, char *argv[]) {
    printf("hello world (pid:%d)\n", (int) getpid());
    int rc = fork();
    if (rc < 0) {
        // fork failed
        fprintf(stderr, "fork failed\n");
        exit(1);
    } else if (rc == 0) {
        // child (new process)
        printf("hello, I am child (pid:%d)\n", (int) getpid());
    } else {
        // parent goes down this path (main)
        int rc_wait = wait(NULL);
        printf("hello, I am parent of %d (rc_wait:%d) (pid:%d)\n",
        rc, rc_wait, (int) getpid());
    }
    return 0;
}
```

```bash
prompt> ./p2
hello world (pid:29266)
hello, I am child (pid:29267)
hello, I am parent of 29267 (rc_wait:29267) (pid:29266)
prompt>
```

El proceso padre llama a `wait()` para retrasar su ejecución hasta que el hijo termine de ejecutarse. Cuando el hijo termina, `wait()` regresa al padre.
Agregando la llamada `wait()` en el código hace que la salida sea determinista.
En el código, sabemos que el hijo siempre imprimirá primero, ya que si primero se ejecuta el padre, antes de imprimir llama a `wait()`; y la system call no retornara hasta que el hijo no finalice, y solo entonces continua su ejecución e imprime por pantalla.

### System Call `exec()`

Esta system call es útil cuando queremos poner en ejecución un programa que (es diferente desde el programa de llamadas¿?)(that is different from the calling program). Por ejemplo: llamando a `fork()` en `p2.c` es solo útil si queremos mantener en ejecución copias del mismo programa. Sin embargo, a menudo vamos a querer ejecutar un programa diferente un programa, y `exec()` hace justo eso.

### `p3.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <sys/wait.h>

int main(int argc, char *argv[]) {
  printf("hello world (pid:%d)\n", (int) getpid());
  int rc = fork();
  if (rc < 0) {
    // fork failed; exit
    fprintf(stderr, "fork failed\n");
    exit(1);
  } else if (rc == 0) { // child (new process)
    printf("hello, I am child (pid:%d)\n", (int) getpid());
    char *myargs[3];
    myargs[0] = strdup("wc");
    // program: "wc" (word count)
    myargs[1] = strdup("p3.c"); // argument: file to count
    myargs[2] = NULL;
    // marks end of array
    execvp(myargs[0], myargs); // runs word count
    printf("this shouldn’t print out");
  } else {
    // parent goes down this path (main)
    int rc_wait = wait(NULL);
    printf("hello, I am parent of %d (rc_wait:%d) (pid:%d)\n",
    rc, rc_wait, (int) getpid());
  }
  return 0;
}
```

```bash
prompt> ./p3
hello world (pid:29383)
hello, I am child (pid:29384)
  29  107 1030  p3.c
hello, I am parent of 29384 (rc_wait:29384) (pid:29383)
prompt>
```

El proceso hijo llama a `execvp()` para ejecutar el programa `wc`, el cual es un programa contador de palabras (word counter).
Dado el nombre de un ejecutable (por ejemplo: `wc`), y algunos argumentos (por ejemplo: ***p3.c***), carga el código y los datos estáticos de ese ejecutable y sobrescribe su segmento de código actual (y sus datos estáticos) con el nuevo programa; el ***heap*** y el ***stack*** y otras partes del espacio de memoria del programa son re-inicializadas. Entonces el SO simplemente ejecuta ese programa, pasandole todos los argumentos como el `argv` del proceso. Por lo tanto, esta system call no crea un nuevo proceso; mas bien, transforma el actual programa en ejecución (antes ***p3***) en diferentes programas en ejecución (`wc`). Después de la llamada a `exec()`, es como si ***p3*** nunca se hubiera ejecutado; una llamada exitosa a `exec()` ***NUNCA RETORNA***.

### ¿Porque? Motivación de la API

La separación de `fork()` y `exec()` es esencial para la ***construcción*** de una ***shell UNIX***, porque esto le permite al shell ejecutar código ***después*** de la llamada `fork()` pero ***antes*** de la llamada `exec()`.
El ***shell*** es solo un programa de usuario. Te muestra un ***prompt*** y entonces espera a que tipees algo. Entonces tipeas un comando (o sea un programa ejecutable, mas cualquier argumento), llama a `fork()` para crear un nuevo proceso hijo para ejecutar el comando, llama a alguna variante de `exec()` para poner en ejecución el comando, y entonces espera que el comando complete su ejecución llamando a `wait()`. Cuando el hijo termina, el shell regresa del `wait()` e imprime de nuevo el ***prompt***, listo para el siguiente comando.
La separación de `fork()` y `exec()` le permite al shell hacer un montón de cosas útiles con bastante facilidad. Por ejemplo:

```bash
prompt> wc p3.c > newfile.txt
```

La salida del programa `wc` es redirigida al archivo *newfile.txt*. La forma en la que el shell logra esto es bastante simple: cuando el proceso hijo es creado, antes de llamar a `wait`, el shell cierra la salida estándar y abre el archivo *newfile.txt*. Haciendo esto, cualquier salida de `wc` a punto de ser ejecutado sera enviado al archivo en vez de la pantalla.

### `p4.c`

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <string.h>
#include <fcntl.h>
#include <sys/wait.h>

int main(int argc, char *argv[]) {
  int rc = fork();
  if (rc < 0) {
    // fork failed
    fprintf(stderr, "fork failed\n");
    exit(1);
  } else if (rc == 0) {
    // child: redirect standard output to a file
    close(STDOUT_FILENO);
    open("./p4.output", O_CREAT|O_WRONLY|O_TRUNC, S_IRWXU);

    // now exec "wc"...
    char *myargs[3];
    myargs[0] = strdup("wc");
    // program: wc (word count)
    myargs[1] = strdup("p4.c"); // arg: file to count
    myargs[2] = NULL;
    // mark end of array
    execvp(myargs[0], myargs); // runs word count
  } else {
    // parent goes down this path (main)
    int rc_wait = wait(NULL);
  }
  return 0;
}
```

```bash
prompt> ./p4
prompt> cat p4.output
  32  109 846 p4.c
prompt>
```

 Este ejemplo hace exactamente eso. La razón por la que la redirección funciona se debe a una ***suposición*** sobre como el SO maneja los ***files descriptors***. Los sistemas UNIX empiezan buscando file descriptors libres. En este caso, ***STDOUT_FILENO*** sera el primero en estar disponible y por lo tanto es asignado cuando `open()` es llamado. Subsecuente escrito por el proceso hijo en la salida estándar del file descriptor, por ejemplo: para las rutinas como `printf()`, se dirigirán de forma transparente al archivo recién abierto en lugar de la pantalla.
 Notar dos cosas sobre la salida:
 1. Cuando se ejecuta p4, es como si nada hubiera pasado; el shell solo imprime el command prompt e inmediatamente esta listo para el siguiente comando. De hecho el programa p4 llamo a `fork()` para crear un nuevo hijo, y entonces ejecuto el programa `wc` a través de una llamada a `execvp()`. No se ve impresa ninguna salida en la pantalla porque a sido redireccionada al archivo ***p4.output***. 
 2. Cuando ejecutamos `cat` con el archivo de salida, se imprime toda la salida esperada del `wc`.
Los ***pipes*** de UNIX son implementados de una forma similar, pero con la system call `pipe()`. En este caso, la salida de uno de los procesos es conectada a un ***in-kernel pipe***, o sea a una cola, y la entrada de otro proceso es conectada al mismo pipe; por lo tanto, la salida de un proceso aparenta ser usada como la entrada del siguiente, y una larga y útil cadena de comandos pueden ser insertadas juntas. como un ejemplo simple, consideremos buscar una palabra en un archivo, y contar cuantas veces esta esa palabra; los pipes y la utilidades `grep` y `wc`, es fácil; solo hay que tipear `grep -o foo file | wc -l` en un command prompt

### Usuarios y Control de procesos

Existen otras formas de interactuar con los procesos. Por ejemplo: la system call `kill()`, es usada para enviar señales a un proceso, incluyendo directivas como pausar, morir, y otros imperativos útiles. En muchas shell de UNIX ciertas combinaciones de teclas son especificadas para llevar una señal especifica al actual proceso en ejecución; por ejemplo: `control + c` envía una señal ***SIGINT*** (interrumpir) al proceso (normalmente terminandolo) y `control + z` envía una señal ***SIGTSTP*** (stop) pausando el proceso en el medio de la ejecución.
¿Quien puede enviar una señal a un proceso, y quien no?. Los sistemas que usamos pueden tener múltiples personas unsandolos al mismo tiempo; si una de esas personas puede arbitrariamente enviar señales como ***SIGINT*** para interrumpir el proceso, la usabilidad y seguridad del sistema estaría comprometida. Como resultado, los sistemas modernos incluyen una fuerte concepción de la noción de ***usuario***. El usuario, después de ingresar un contraseña para establecer credenciales, inicia sesión para ganar accesos a los recursos del sistema. Entonces el usuario puede ejecutar uno o muchos procesos, y ejercer control total sobre ellos (pausarlos, matarlos, etc). Los ***usuarios*** generalmente solo pueden controlar sus propios procesos; el trabajo del OS es repartir recursos a cada usuario para cumplir con los objetivos generales del sistema.

### Herramientas útiles

- Usando el comando `ps` pueden ver que proceso se están ejecutando (leer la **man pages*** para ver algunas ***flags*** útiles).
- La herramienta `top` muestra por pantalla los procesos del sistema y cuanta CPU y otros recursos están usando.
- El comando `kill` puede ser usado para enviar arbitrariamente señales a procesos.