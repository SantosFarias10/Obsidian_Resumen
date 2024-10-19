Instrucciones para instalar xv6 riscv y emularlo.

1. Clonar el repositorio original de xv6-riscv:

```bash
$ git clone git@github.com:mit-pdos/xv6-riscv.git

$ cd xv6-riscv; git reset --hard f5b93ef; cd ..
```

2. Copiar los archivos clonados al repositorio de Bitbucket asignado a su grupo.

```bash
git clone https://user@bitbucket.org/sistop-famaf/so24labXg36.git

cp -a xv6-riscv/* so24labXg36

rm -rf xv6-riscv
```

3. Hacer el primer commit+push del proyecto

```bash
cd so24labXg36

git tag -a original_code -m "Original xv6 code"

git add *  // Fijense que no hayan compilado código fuente. Hagan make clean

git commit -m 'Original xv6 code'

git push origin; git push origin --tags
```

4. Instalar qemu

```bash
sudo apt-get install qemu-system-riscv64 gcc-riscv64-linux-gnu
```

5. Compilar e iniciar xv6: posicionarse en el directorio so24labXg36 (debe haber un archivo “Makefile”) y ejecutar el sistema usando qemu. 
   
   Como en este laboratorio (lab 3) haremos hincapié en mediciones de rendimiento, 
   **Importante**: Aunque `xv6-riscv` soporta múltiples procesadores, debemos ejecutar nuestras mediciones lanzando la máquina virtual con un único procesador utilizando

```bash
make CPUS=1 qemu
```