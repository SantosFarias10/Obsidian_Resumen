
Para ver cómo el planificador de `xv6-riscv` afecta a los distintos tipos de procesos en la práctica, deberán integrar a `xv6-riscv` los programas de espacio de usuario `iobench` y `cpubench` (que adjuntamos en el aula virtual). Estos programas realizan mediciones de operaciones de escritura/lectura y operaciones de cómputo, respectivamente.

Las funciones son:

  * [iobench](iobench.md)(int N): Ejecuta N veces un experimento donde cuenta operaciones de entrada/salida. Cada experimento abre un archivo para escritura y luego escribe el contenido de un buffer, y repite las operaciones para lectura, es decir, operaciones de entrada/salida.

  * [cpubench](cpubench.md)(int N): Ejecuta N veces un experimento donde cuenta operaciones de cómputo. Cada ciclo de medición realiza varias multiplicaciones de matrices MxM, es decir, operaciones de cómputo intensivo.