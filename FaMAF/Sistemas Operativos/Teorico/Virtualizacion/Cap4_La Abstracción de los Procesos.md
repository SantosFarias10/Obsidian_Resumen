## Conceptos Claves
- ***Tiempo Compartido de CPU***(Time Sharing of CPU): Es una técnica básica usada por el **SO** para compartir recursos consiste en permitir que el recurso sea usado un ratito por una entidad y luego otro ratito por otra, y así sucesivamente, el recurso en cuestión puede ser compartido por muchos. Permite a los usuarios correr tantos procesos de forma concurrente como ellos quieran; el costo potencial es el desempeño, cada proceso se ejecutara mas lento si las CPU deben ser compartidas.
- ***Espacio Compartido*** (Space Sharing): Donde un recurso es dividido (En el espacio) entre quienes quieran usarlo.
- ***Mecanismo***: Son métodos de bajo nivel o protocolos que implementan una pieza necesaria de funcionalidad.
- ***Políticas***: Son algoritmos para tomar algún tipo de decisión dentro del **SO**, como que programa debería ejecutar el **SO**.
- ***Proceso***: Abstracción que el **SO** proporciona de un programa en ejecución.
	- ***Maquina de estado***(Machine State): Lo que un programa puede leer o actualizar mientras se esta ejecutando. Componentes de la maquina de estado que tiene un **proceso**: Su memoria. Las instrucciones viven en la memoria => la memoria que el proceso puede direccionar (espacio direcionable) es parte del proceso.

## ¿Que cosas debe tener incluido cualquier interfaz de un SO?

Estas APIs están disponibles en cualquier SO moderno:
- ***Crear*** (Create): Método para crear un proceso nuevo.
- ***Destruir*** (Destroy): Método para destruir un proceso / forzar la detención de un proceso.
- ***Esperar*** (Wait): Método para esperar un proceso (Por Ejemplo: Esperarlo antes de terminar la ejecución).
- ***Control Miscelaneo*** (Miscellaneous Control): Método para suspender un proceso por un tiempo y, detener su ejecución y después continuar ejecutándolo.
- ***Estado*** (Status): Método para obtener información de estado de un proceso (Por Ejemplo: Por cuanto tiempo se estuvo ejecutando o su estado interno).


![[Figure 4.1: Cargando: De Programa a Proceso.png]]
Figura 4.1: ***Cargando***: De un Programa a Proceso.

## ¿Como se transforma un programa (estático) a un proceso dinámico?

1.  Carga el código y Data estática en la memoria en el espacio direccionable. Los programas residen en un disco en algún formato ejecutable, el ***SO lee**** esos bytes ***y los pone en la memoria***.
2.  Se hace `alloc()` de la memoria para el run-time stack. El SO asigna la memoria y se la da al proceso e inicializa el stack con argumentos (como `argc` y `argv`).
3.  Se hacen otras inicializaciones relacionadas con el ***Input / Output*** (I/O)
4.  Le queda empezar a correr el programa desde el main(). El SO le transfiere el control de la CPU al recién creado proceso y así el programa comienza su ejecución.

## Estados de un proceso

- ***En ejecución*** (Running): Un proceso esta **ejecutándose** en un procesador. Es decir, esta ejecutando instrucciones.
- ***Listo*** (Ready): Un proceso esta **listo** para ejecutarse, pero por alguna razón el SO ha decidido no ejecutarlo en ese momento.
- ***Bloqueado*** (Blocked): El proceso a realizado algún tipo de operacion que lo hace no esta listo para ejecutarse hasta que algún otro evento tome lugar (Por ejemplo: Cuando un proceso inicia una petición de I/O al disco pasa a estar bloqueado y por lo tanto algún otro proceso puede usar el procesador).

![[Figure 4.2: Process: State Transitions.png]]
Figure 4.2: Process: State Transitions