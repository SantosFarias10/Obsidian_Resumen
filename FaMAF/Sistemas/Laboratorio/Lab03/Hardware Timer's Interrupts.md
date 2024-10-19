Xv6 usa interrupciones de tiempo "Timer Interrupts" para mantener su reloj y para habilitarlo para cambiar entre procesos _compute-bound_, este cambio sucede en [[yield]] al llamar *usertrap* y *kerneltrap* causando el cambio. Esta interrupción de tiempo viene de un reloj en hardware ligado a cara RISC-V CPU. Donde xv6 programa este reloj en hardware para interrumpir cada CPU periódicamente.

RISC-V requiere que la interrupción de tiempo suceda en machine mode. El código ejecutado en modo maquina empieza en el archivo `start.c`, antes del `main`, donde se configura para recibir las interrupciones de tiempo. En este archivo es donde se genera el "Quantum" para el scheduler programando el CLINT hardware (core-local interruptor), ademas se configura una "Scratch Area" para ayudar al **handler** del timer interrupt a manejar el guardado en registros y el address del registro CLINT. Por ultimo *start* configura `mtvec` para `timervec` y habilita las interrupciones de tiempo.

Estas interrupciones de tiempo pueden suceder en cualquier momento, inclusive si se esta corriendo código en user o kernel mode.

El machine-mode timer interrupt handler es el `timervec`, este guarda algunos registros en la **scratch area** preparada por *start*, diciéndole a *CLINT* cuando generar la siguiente timer interrupt, pregunta a RISC-V de alzar un software interrupt, restaurar registros y retornar.

==Falta terminar de leer la sección de Real World==