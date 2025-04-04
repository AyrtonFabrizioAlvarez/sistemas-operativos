# Practica 2
## System Calls

### 1.  ¿Qué es una System Call? ¿Para qué se utiliza?  
Una System Call es una interfaz que permite que los programas de usuario soliciten servicios al sistema operativo. Se utiliza para acceder a funcionalidades del kernel como manejo de archivos, memoria, procesos y dispositivos, ya que el modo usuario no tiene acceso directo a recursos protegidos.
### 2.  ¿Para qué sirve la macro syscall? Describa el propósito de cada uno de sus parámetros.  
La macro syscall permite invocar directamente una system call especificando su número (long number) y sus parámetros. Es útil para hacer llamadas al sistema que no tienen función wrapper o cuando se necesita un control más bajo nivel.  
***¿Por qué es importante?***  
La macro syscall permite un acceso controlado y explícito a llamadas del sistema. Es particularmente útil para estudiar o probar llamadas del sistema nuevas o no estándar, o para evitar dependencias de alto nivel. 
### 3.  Ejecute  el siguiente comando e identifique el propósito de cada uno de los archivos que encuentra `ls -lh /boot | grep vmlinuz`  
***¿Qué es vmlinuz?***  
El nombre vmlinuz hace referencia al núcleo de Linux comprimido (VM = Virtual Memory, z = comprimido). Es el archivo binario que contiene el kernel del sistema operativo y que se carga en memoria al arrancar la computadora.  

**El comando muestra los archivos del kernel de Linux en /boot.**  
- **vmlinuz** apunta al kernel en uso (6.8.0-57).
- **vmlinuz.old** apunta al kernel anterior (6.8.0-52).
- También hay versiones más viejas (6.5.0-18, 6.8.0-52) que pueden mantenerse como respaldo.
### 4.  Acceda al codigo fuente de GNU Linux, sea visitando https://kernel.org/ o bien trayendo el código del kernel(cuidado, como todo software monolítico son unos cuantos gigas) `git clone https://github.com/torvalds/linux.git`
### 5.  ¿Para qué sirven el siguiente archivo? 
### a.  arch/x86/entry/syscalls/syscall_64.tbl 
### 6.  ¿Para qué sirve la herramienta strace? ¿Cómo se usa? 
### 7.  ¿Para qué sirve la herramienta ausyscall? ¿Cómo se usa? 