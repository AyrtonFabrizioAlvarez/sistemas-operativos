# Practica 2
## System Calls Teoría

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
- **vmlinuz** apunta al kernel en uso.
- **vmlinuz.old** apunta al kernel anterior.
- También hay versiones más viejas que pueden mantenerse como respaldo.
### 4.  Acceda al codigo fuente de GNU Linux, sea visitando https://kernel.org/ o bien trayendo el código del kernel(cuidado, como todo software monolítico son unos cuantos gigas) `git clone https://github.com/torvalds/linux.git`
### 5.  ¿Para qué sirven el siguiente archivo? `arch/x86/entry/syscalls/syscall_64.tbl`  
El archivo syscall_64.tbl del kernel de Linux asocia números de system calls con las funciones del kernel en la arquitectura x86_64. Es fundamental para que el kernel pueda identificar y ejecutar correctamente las llamadas al sistema.
### 6.  ¿Para qué sirve la herramienta strace? ¿Cómo se usa?  
`strace` es una herramienta para rastrear llamadas al sistema (system calls) en Linux. Se usa para depuración, optimización y seguridad. 
<br>

***Para ejecutar un programa con strace:***  
```bash
strace ./mi_programa
```  
<br>

***Para analizar un proceso en ejecución:***  
```bash
strace -p <PID>
```
<br>

***Para guardar la salida en un archivo:***  
```bash
strace -o salida.txt ./mi_programa
```  
<br>

***Para filtrar solo un tipo de system call:***  
```bash
strace -e open ./mi_programa
```  
<br>

***Para contar cuántas veces se ejecuta cada system call:***  
```bash
strace -c ./mi_programa
```  
<br>

### 7.  ¿Para qué sirve la herramienta ausyscall? ¿Cómo se usa?  
`ausyscall` es una herramienta de auditoría en Linux que permite consultar los números y nombres de system calls en diferentes arquitecturas.
<br>

***Para listar todas las system calls:***  
```bash
ausyscall --dump
```  
<br>

***Para ver el nombre de una system call dado su número:***  
```bash
ausyscall 60    # retorna "exit"
```  
<br>

***Para obtener el número de una system call dado su nombre:***  
```bash
ausyscall exit  # retorna 60
```  
<br>


## System Calls Practica  

### Mirando el código anterior `my_sys_call.c`,  investigue y responda lo siguiente? 
- **¿Para qué sirven los macros SYS_CALL_DEFINE?**  
Los macros SYSCALL_DEFINE se usan en el kernel de Linux para definir nuevas llamadas al sistema  
    - `SYSCALL_DEFINE1(nombre, tipo1, arg1)`: Define una syscall con un argumento.
    - `SYSCALL_DEFINE2(nombre, tipo1, arg1, tipo2, arg2)`: Define una syscall con dos argumentos.
- **¿Para que se utilizan la macros for_each_process y for_each_thread?**  
Ambas macros recorren estructuras del scheduler para obtener información sobre procesos e hilos en ejecución.

    - `for_each_process(task)`
      - Recorre todos los procesos activos en el sistema.
      - task es un puntero a struct task_struct, que representa cada proceso.
      - Se usa en get_task_info para listar los procesos en ejecución.

    - `for_each_thread(task, thread)`
      - Recorre todos los hilos dentro de un proceso.
      - task representa el proceso padre.
      - thread representa cada uno de sus hilos.
      - Se usa en get_threads_info para listar hilos dentro de cada proceso.
- **¿Para que se utiliza la función copy_to_user?**  
    - `copy_to_user(destino_usuario, origen_kernel, tamaño)`
      - Copia datos del espacio del kernel al espacio de usuario.
      - Evita que los programas de usuario accedan directamente a memoria del kernel, protegiendo la seguridad del sistema.
      - Devuelve 0 si la copia fue exitosa o -EFAULT si hubo error.
- **¿Para qué se utiliza la función printk?, ¿porque no la típica printf?**  
  - `printk` es la versión de `printf` para el kernel de Linux.
  - No se puede usar printf en el kernel porque este no tiene acceso directo a stdio (entrada/salida estándar).
  - `printk` registra mensajes en el buffer del kernel, accesibles con `dmesg`.
  - Se usa para depuración y registro de eventos en el kernel.
- **Podría explicar que hacen las sytem call que hemos incluido?**
    - `my_sys_call`: Solo imprime un mensaje.
    - `get_task_info`: Lista los procesos activos.
    - `get_threads_info`: Lista hilos dentro de cada proceso.





 
 
 


