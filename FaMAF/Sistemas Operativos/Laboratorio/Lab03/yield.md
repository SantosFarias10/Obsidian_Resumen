==Resumido por GPT==
En xv6, la función **yield** es una parte fundamental del *scheduler* (planificador de procesos) y permite que un proceso ceda voluntariamente la CPU para que otro proceso pueda ejecutarse. Esta función es importante en sistemas multitarea, donde los procesos deben turnarse para usar la CPU.

Cuando un proceso llama a yield, básicamente está diciendo: "Hice suficiente por ahora, puedes dejar que otro proceso utilice la CPU." Luego, el *scheduler* tomará el control y seleccionará el siguiente proceso a ejecutar.

Normalmente yield es llamado cuando un proceso realiza una operación de IO, dejando así de usar la CPU mientras espera a que se complete esta acción de IO. Y como deja de necesitar de la CPU, pasa a estar bloqueado, liberando la CPU para otros procesos.

En resumen, `yield()` es una forma en la que un proceso "cede" la CPU voluntariamente para permitir que otros procesos se ejecuten, ayudando a la distribución equitativa de los recursos del sistema.

### Función `yield()` en xv6

1. **Bloqueo del proceso actual**:  
   El proceso actual se marca como "runnable" (ejecutable), pero no ejecutándose actualmente, lo que permite que otros procesos puedan ser seleccionados por el *scheduler*.
   ```c
   p->state = RUNNABLE;
   ```

2. **Cambio de contexto**:  
   Luego de marcar el proceso como "runnable", se llama a `sched()`, que efectúa el cambio de contexto.

   ```c
   sched();
   ```

   La función `sched()` invoca al *scheduler* para seleccionar otro proceso que esté en estado `RUNNABLE` y realizar el cambio de contexto. Internamente, `sched()` utiliza la función `swtch()` para guardar el estado del proceso actual y restaurar el de otro proceso.

3. **Retomar la ejecución**:  
   Una vez que el *scheduler* decida que el proceso actual puede volver a ejecutarse (cuando le toque su turno), se restaurará su contexto, y la ejecución continuará justo después de donde dejó la llamada a `yield()`.

### Importancia de `yield()`

- **Evitar monopolización de la CPU**: `yield()` evita que un solo proceso use toda la CPU sin dar oportunidad a otros procesos de ejecutarse. Esto es crucial para mantener un sistema multitarea eficiente.
- **Colaboración entre procesos**: Si un proceso está esperando que ocurra un evento, puede llamar a `yield()` para ceder la CPU hasta que ese evento ocurra, en lugar de simplemente esperar y bloquear la ejecución de otros procesos.