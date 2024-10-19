# Datos Útiles
- [Enunciado](https://docs.google.com/document/d/1C_5iZmFkeDRUfSxVB7gFeBoTY_vN7Ff91TeO2G89RdQ/edit#heading=h.jkzlxmuvg080)
- Pagina para hacer el [informe en el formato](https://readme.so/es/editor) que pide el lab.
- Git de [xv6](https://github.com/mit-pdos/xv6-riscv).
- [[Manual xv6]].
- Como producir [Histogramas](https://pesin.space/posts/2020-10-31-histograms/) en la terminal.

# Completado 
- [x] [[Instalación xv6]]
- [x] Primera Parte: Estudiando el planificador de xv6-riscv
- [ ] Segunda Parte: Medir operaciones de cómputo y de entrada/salida
	- [ ] Experimento 1: ¿Cómo son planificados los programas iobound y cpubound?
	- [ ] Experimento 2: ¿Qué sucede cuando cambiamos el largo del quantum?
- [ ] Tercera Parte: Asignar prioridad a los procesos
- [ ] Cuarta Parte: Implementar MLFQ
- [ ] Puntos estrella

# Objetivos
El planificador apropiativo de `xv6-riscv` utiliza un algoritmo sencillo para distribuir tiempo de procesador entre los procesos en ejecución, pero esto tiene un costo aparejado. Los objetivos de este laboratorio son:

- Estudiar el funcionamiento del [[Scheduler original de xv6-riscv]].
- Analizar los procesos que se benefician/perjudican con esta decisión de diseño.
- Desarrollar una implementación reemplazando la política de planificación por una propia que deberá respetar ciertas condiciones.
- Analizar cómo la nueva política afecta a los procesos en comparación con el planificador original.

# Implementación
- Primera Parte: [[Estudiando el Planificador de xv6-riscv]].
- Segunda Parte: [[Medir operaciones de cómputo y de IO]].
	- [[Experimeton 1 ¿Cómo son planificados los programas iobound y cpubound?]].
	- Experimento 2: [[¿Qué sucede cuando cambiamos el largo del quantum?]].
- Tercera Parte: [[Asignar prioridad a los procesos]].
- Cuarta Parte: [[Implementar MLFQ]].
- Puntos estrella