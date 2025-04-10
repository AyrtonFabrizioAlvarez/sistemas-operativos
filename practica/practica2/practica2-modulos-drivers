# Practica 2
## Módulos y Drivers Teoría
### 1.  ¿Cómo se denomina en GNU/Linux a la porción de código que se agrega al kernel en tiempo de ejecución? ¿Es necesario reiniciar el sistema al cargarlo?. Si no se pudiera utilizar esto. ¿Cómo deberíamos hacer para proveer la misma funcionalidad en Gnu/Linux? 
- La porción de código que se puede agregar al kernel de GNU/Linux en tiempo de ejecución se denomina módulo del kernel o LKMs (Loadable Kernel Modules). La utilidad principal es la dinamismo y flexibilidad: uno puede cargar (`insmod`, `modprobe`) o descargar (`rmmod`) un módulo en tiempo real, facilitando el mantenimiento y pruebas, y reduciendo el tiempo fuera de servicio del sistema.  
- No, no es necesario reiniciar. Esa es justamente una de las ventajas: se puede agregar funcionalidad al kernel mientras el sistema está corriendo.  
- En ese caso, toda la funcionalidad requerida debería estar compilada directamente en el kernel. Para hacerlo, se tendría que:
  - Modificar la configuración del kernel (`make menuconfig`).
  - Recompilar el kernel completo con la funcionalidad deseada.
### 2.  ¿Qué es un driver? ¿Para qué se utiliza? 
Un driver es un programa o conjunto de rutinas de bajo nivel que permite al sistema operativo comunicarse e interactuar con un dispositivo de hardware específico, también actúa como intermediario ya que traduce las instrucciones genéricas del sistema operativo en comandos específicos que el hardware puede entender, y viceversa.  
**<u>Los drivers son esenciales para:</u>**
- Detectar y administrar el hardware.
- Permitir que las aplicaciones usen dispositivos sin preocuparse por los detalles técnicos del hardware.
- Garantizar la portabilidad: el mismo sistema puede funcionar con distintos dispositivos si tiene el driver correspondiente.
### 3.  ¿Por qué es necesario escribir drivers? 
**Diversidad de Hardware:** Sin un driver, el sistema operativo no podría interactuar con ellos de manera estándar, ya que cada dispositivo tiene un comportamiento único que debe ser gestionado de forma especializada.

**Abstracción:** Los drivers proporcionan una capa de abstracción entre el hardware y el sistema operativo. Sin esta abstracción, cada programa tendría que "saber" cómo comunicarse directamente con cada dispositivo.

**Portabilidad**: Los drivers permiten que el sistema operativo sea más portátil. Sin ellos, el sistema operativo debería estar diseñado específicamente para cada dispositivo.

**Optimización y Control:** Los drivers también son necesarios para optimizar el uso del hardware, aprovechando al máximo sus capacidades y controlando aspectos específicos del mismo, como el manejo de la memoria, la gestión de energía o la sincronización de operaciones.

**Actualización y Flexibilidad:** Los drivers pueden actualizarse sin necesidad de cambiar el núcleo del sistema operativo, permitiendo mejoras en el rendimiento, corrección de errores o soporte de nuevos dispositivos sin afectar a la estabilidad global del sistema.
### 4.  ¿Cuál es la relación entre módulo y driver en GNU/Linux? 
- Un driver es un programa que **permite al sistema usar hardware.**
- Un módulo es una **pieza de código que se puede agregar al kernel en tiempo de ejecución.**
- **En GNU/Linux, muchos drivers están implementados como módulos del kernel**, por lo que un driver suele ser un tipo de módulo.
### 5.  ¿Qué implicancias puede tener un bug en un driver o módulo? 
**Caída del sistema (kernel panic):**
Un error grave puede provocar un kernel panic, una situación en la que el sistema operativo detecta un fallo irrecuperable y se detiene completamente para evitar dañar los datos o el hardware.

**Corrupción de memoria:** Como el módulo tiene acceso sin restricciones a la memoria del sistema, un puntero mal manejado puede sobrescribir regiones críticas de memoria, afectando el funcionamiento del sistema o corrompiendo datos.

**Fallas intermitentes o impredecibles:** Algunos bugs no causan un fallo inmediato, sino comportamientos erráticos: pérdidas de conectividad, errores de lectura/escritura, mal funcionamiento de dispositivos, etc.

**Vulnerabilidades de seguridad:** Un bug puede ser explotado por un atacante para ejecutar código arbitrario en modo kernel, lo que le daría control total del sistema, con consecuencias potenciales como robo de datos, instalación de rootkits, etc.

### 6.  ¿Qué tipos de drivers existen en GNU/Linux? 
Los drivers se pueden clasificar según el **tipo de hardware o funcionalidad que manejan.**:

1. **Drivers de dispositivos de carácter**  
Permiten el acceso secuencial o por flujo, como si fuera un archivo.  
**Ejemplos:** terminales, puertos serie, dispositivos de audio.  
Se accede a través de archivos en `/dev` como `/dev/ttyS0`.  

2. **Drivers de dispositivos de bloque**  
Acceden al hardware en bloques de datos (como sectores de disco). Se utilizan para almacenamiento.  
**Ejemplos:** discos duros, unidades SSD, memorias USB.  
Se accede a través de archivos como `/dev/sda`, `/dev/nvme0n1`.  

3. **Drivers de red (network drivers)**  
Gestionan interfaces de red y protocolos.  
**Ejemplos:** controladores de tarjetas de red Ethernet o Wi-Fi.  
Aparecen como interfaces como `eth0`, `wlan0`.  

4. **Drivers de sistema de archivos (filesystem drivers)**  
Permiten al kernel entender diferentes formatos de sistemas de archivos.  
**Ejemplos:** `ext4`, `btrfs`, `ntfs`.  
Pueden ser integrados en el kernel o cargados como módulos.  

5. **Drivers de dispositivos gráficos**  
Controlan placas de video (GPU).  
Pueden ser libres (como nouveau para NVIDIA) o propietarios (como los drivers oficiales de NVIDIA o AMD).  
Impactan directamente en el rendimiento gráfico, soporte de aceleración, etc.  

6. **Drivers virtuales o pseudo-dispositivos**  
No representan hardware físico, sino interfaces virtuales.   
**Ejemplos:** `/dev/null`, `/dev/random`, `loop devices`.  

7. **Drivers de dispositivos de entrada**  
Gestionan teclado, mouse, touchpad, etc.  
Se conectan al subsistema de entrada (`evdev`, `libinput`).  

### 7.  ¿Qué hay en el directorio /dev? ¿Qué tipos de archivo encontramos en esa ubicación? 
El directorio `/dev` en GNU/Linux contiene archivos especiales que representan dispositivos del sistema. Es una interfaz entre el usuario y el hardware, porque en Linux todo es un archivo, incluso los dispositivos físicos.  
Cuando un proceso desea interactuar con un dispositivo, lo hace leyendo o escribiendo en su archivo correspondiente en `/dev`.

**<u>Tipos de archivos que encontramos en `/dev`:</u>**  

**Archivos de dispositivo de carácter (c)**:  
Usados para dispositivos que se acceden como un flujo de bytes.  
**Ejemplos:** `/dev/tty`, `/dev/console`.  

**Archivos de dispositivo de bloque (b)**:  
Usados para dispositivos que manejan datos en bloques de bytes.  
**Ejemplos:** discos rígidos `/dev/sda`, memorias USB, particiones `/dev/sda1`.  

**Enlaces simbólicos**:  
Algunos dispositivos tienen alias para facilitar su identificación.  
**Ejemplo:** /dev/cdrom puede ser un enlace a /dev/sr0.  

**Dispositivos virtuales o pseudo-dispositivos**:  
No representan hardware real, sino funciones útiles del sistema.  
**Ejemplos:**  
- `/dev/null`: "agujero negro" donde se descartan datos.
- `/dev/zero`: genera una secuencia infinita de ceros.
- `/dev/random` y `/dev/urandom`: generan números aleatorios.

**Dispositivos gestionados dinámicamente**:  
Sistemas como `udev` gestionan `/dev` de forma dinámica, **creando y eliminando dispositivos conforme se conectan o desconectan.**  

### 8.  ¿Para qué sirven el archivo /lib/modules/<version>/modules.dep utilizado por el comando modprobe? 
El archivo `/lib/modules/<versión>/modules.dep` lista las dependencias entre módulos del kernel.  
**Ejemplo:** `nombre_del_módulo.ko: dependencia1.ko dependencia2.ko`  
Es utilizado por modprobe para cargar automáticamente los módulos necesarios en el orden correcto.  
### 9.  ¿En qué momento/s se genera o actualiza un initramfs? 
**Durante la instalación del sistema operativo**:  
Cuando se instala una distribución GNU/Linux, el instalador **genera un initramfs inicial** para que el sistema pueda arrancar.  

**Cuando se instala o actualiza el kernel**:  
Al instalar un nuevo kernel (por ejemplo, mediante `apt`), se **genera un nuevo initramfs** correspondiente a esa versión de kernel.  

**Cuando se cambian drivers o configuraciones críticas**:  
Si se agregan drivers que son necesarios para arrancar (por ejemplo, drivers de disco, RAID, LVM, cifrado), se debe **regenerar el initramfs**.  

**Cuando se ejecuta manualmente el comando de regeneración**:  
`update-initramfs -u` (en distribuciones Debian/Ubuntu)  
### 10.  ¿Qué módulos y drivers deberá tener un initramfs mínimamente para cumplir su objetivo? 
**Módulos de drivers esenciales del kernel para que el kernel pueda detectar y montar el disco raíz**:

- **Drivers de almacenamiento**:  
    Para controlar el tipo de controlador SATA, NVMe, SCSI, etc.  
    **Ejemplos:** `ahci` (controlador SATA), `nvme`, `sd_mod` (disco SCSI), `ext4`, `xfs`. (si el sistema de archivos raíz usa esos formatos)  

- **Sistema de archivos**:  
    El módulo que permite interpretar el sistema de archivos raíz.  
    **Ejemplos:** `ext4`, `xfs`, `btrfs`.

- **Drivers de dispositivos de bloques**:  
    Para acceder correctamente a discos, particiones y bloques de datos.  

- **Controladores de bus**:  
    Como `pci`, `usb`, si el dispositivo raíz es accesible por estos buses.  

**Herramientas básicas de usuario (uspace)**:
- Shell mínima (`busybox`, por ejemplo)
- Scripts de arranque (`init` del initramfs)
- Utilidades como `mount`, `ls`, `udevadm`, etc.  

**Archivos de configuración**:   
- `init`: script principal que ejecuta el montaje y pasa el control al sistema raíz.  
- Hooks o scripts para detectar dispositivos, montar, iniciar LVM/RAID, desbloquear cifrado, etc.  

## Módulos y Drivers Práctica Básica


### 2.​ Crear el archivo Makefile con el siguiente contenido:
`obj-m := memory.o`  
**Responda lo siguiente:**  
- **Explique brevemente cual es la utilidad del archivo Makefile.**
  - `obj-m` indica que se está construyendo un módulo externo al kernel, no compilado dentro del árbol completo del kernel.
  - `memory.o` es el archivo objeto que se generará al compilar `memory.c`.
  - se está diciendo: "quiero construir un módulo del kernel llamado memory.ko a partir de memory.c".
- **¿Para qué sirve la macro MODULE_LICENSE? ¿Es obligatoria?**
  - `MODULE_LICENSE` declara la licencia del módulo del kernel.
  - **No es obligatoria**, pero sin ella:
    - El kernel se marca como tainted.
    - No podés usar símbolos exportados bajo GPL.
  - Es una buena práctica incluirla siempre, sobre todo si el módulo es libre.



### 3.
- **a.​ ¿Cuál es la salida del comando anterior?**  
![ejercicio3](./practica2-ejercicio3.png)  
**Esto indica que el compilador está:**    
  - Compilando tu archivo .c en .o
  - Generando metainformación
  - Enlazando el archivo final .ko (kernel object)
- **b.​ ¿Qué tipos de archivo se generan? Explique para qué sirve cada uno?.**  
    **En el proceso se generan varios archivos. Los principales son:**  

    | Archivo	     | Función                                                               |
    | -------------- | --------------------------------------------------------------------- |
    | memory.o       | Código objeto intermedio compilado a partir de memory.c.              |
    | memory.mod.o   | Código objeto extendido con información de metadatos del módulo.      |
    | memory.ko      | El módulo compilado final (Kernel Object), listo para cargar.         |
    | modules.order  | Lista de módulos que deben ser instalados en orden.                   |
    | Module.symvers | Tabla de símbolos exportados e importados, útil para enlazar módulos. |

- **c.​ Con lo visto en la Práctica 1 sobre Makefiles, construya un Makefile de manera que si ejecuto:**
  - `make`, nuestro módulo se compila
  - `make clean`, limpia el módulo y el código objeto generado
  - `make run`, ejecuta el programa
 

### 4.​ 
**Para qué sirven el comando insmod y el comando modprobe? ¿En qué sediferencian?**  
- **`insmod` inserta un módulo al kernel manualmente.**  
- **Necesitás pasarle el nombre exacto del archivo `.ko`, por ejemplo: `insmod memory.ko`.**  
- **No resuelve dependencias automáticamente: si el módulo necesita otros, vos tenés que cargarlos primero.**  

<br>

- **`modprobe` busca el módulo por nombre, por ejemplo: `modprobe memory`:**
- **Resuelve dependencias automáticamente.**
- **Usa archivos como /lib/modules/$(uname -r)/modules.dep para saber qué módulos cargar primero.**
- **También permite remover módulos con modprobe -r.**

### 5.​ Ahora ejecutamos: `$ lsmod | grep memory`
- **a.​ ¿Cuál es la salida del comando? Explique cuál es la utilidad del comando lsmod.**
  - `lsmod` muestra la lista de todos los módulos cargados actualmente en el kernel.
  - ![ejercicio3](./practica2-ejercicio5.png)
  - `memory` - nombre del módulo
  - `8192` - tamaño del módulo en bytes
  - `0` - Veces que está en uso (referencias de otros módulos)
- **b.​ ¿Qué información encuentra en el archivo /proc/modules?**  
  - Archivo virtual que contiene la lista completa de módulos cargados.
  - contiene datos como `nombre`, `tamaño`, `usos`, `dependencias`, `estado`, `dirección`.
- **c.​ Si ejecutamos more /proc/modules encontramos los siguientes fragmentos ¿Quéinformación obtenemos de aquí?:**
  - `memory 8192 0 - Live 0x0000000000000000 (OE)`
    | Campo	 | Significado                                                           |
    | ------ | --------------------------------------------------------------------- |
    | memory |	Nombre del módulo cargado                                            |
    | 8192	 | Tamaño en bytes del módulo                                            |
    | 0	     | Veces que el módulo está siendo usado (referencias por otros módulos) |
    | -	     | No tiene dependencias                                                 |
    | Live	 | El módulo está activo (no está en proceso de carga ni de descarga)    |
    | 0x000  | Dirección base en memoria donde fue cargado                           |
    | (OE)	 | Opciones del módulo:                                                  |
  - `O` - Módulo fue cargado manualmente (no automáticamente por el sistema).
  - `E` - Exporta símbolos (funciones/variables) accesibles para otros módulos.
- **d.​ ¿Con qué comando descargamos el módulo de la memoria?**
  - `rmmod memory`: Si el módulo tiene dependencias cargadas, hay que descargar primero los módulos que dependen de él.
  - `modprobe -r memory`: Este comando también verifica dependencias, y puede ser más seguro en sistemas más complejos.



### 8.​ Responda lo siguiente:
- **a.​ ¿Para qué sirven las funciones module_init y module_exit?. ¿Cómo haría para ver la información del log que arrojan las mismas?.**
  - **`module_init(hello_init)`:**
    - Especifica qué función se ejecuta al cargar el módulo.
  - **`module_exit(hello_exit)`:**
    - Define la función a ejecutar cuando el módulo se descarga.
  - **¿Cómo ver los mensajes del log?:**
    - Los mensajes generados con printk() no se ven por pantalla directa, sino que van al log del kernel. Para verlos usamos el comando `dmesg`

- **b.​ Hasta aquí hemos desarrollado, compilado, cargado y descargado un módulo en nuestro kernel. En este punto y sin mirar lo que sigue. ¿Qué nos falta para tener un driver completo?.**  
**Este código es un módulo básico, pero no hace nada relacionado con hardware. Para ser un driver, debe:**
  - Tener lógica para interactuar con dispositivos reales (ej: disco, teclado, etc.).
  - Registrar una interfaz (por ejemplo, crear un archivo en /dev/, o registrar una estructura file_operations).
  - Implementar funciones para que el sistema pueda leer/escribir desde el dispositivo.
  - Posiblemente manejar interrupciones, registros o buses de hardware.


- **c.​ Clasifique los tipos de dispositivos en Linux. Explique las características de cada uno.**

    | Tipo de dispositivo |	Características principales	                                                 | Ejemplos                  |
    | ------------------- | ---------------------------------------------------------------------------- | ------------------------- |
    | De carácter         |	Acceso secuencial (byte a byte), usa `/dev`, funciones tipo `read`, `write`. | `/dev/tty` `/dev/random`  |
    | De bloque	          | Acceso por bloques, ideal para almacenamiento, usa cache, lectura aleatoria. | `/dev/sda` `/dev/mmcblk0` |
    | De red	          | Acceso a través del stack de red, se manejan con sockets.	                 | `eth0` `wlan0`            |



## Módulos y Drivers Práctica Básica

### 2.​ Responda lo siguiente:
- **a.​ ¿Para qué sirve la estructura ssize_t y memory_fops? ¿Y las funciones register_chrdev y unregister_chrdev?**  
  - `ssize_t`:
    - Es un tipo de dato usado para indicar la cantidad de bytes leídos o escritos por funciones como `read()` o `write()`.
    - Si devuelve un valor negativo, indica error.
    - Si devuelve 0 o más, representa cantidad de bytes procesados.
  - `memory_fops`:
    - Es una estructura `file_operations` que define qué funciones del módulo se invocan cuando un proceso realiza operaciones sobre el dispositivo (como `open`, `read`, `write`, `release`, etc.).
    - Es como el "vínculo" entre las llamadas del espacio de usuario (`read(fd), write(fd)) y el código del driver.
  - `register_chrdev`:
    - Registra un dispositivo de carácter ante el sistema.
    - Le indica al kernel que, cuando un proceso haga operaciones sobre el major number que le pasemos, debe usar nuestra estructura file_operations.
    ```C
    int register_chrdev(unsigned int major, const char *name, const struct file_operations *fops);
    ```
  - `unregister_chrdev`:
    - Desregistra el dispositivo del sistema al descargar el módulo, liberando el major number y evitando que se mantenga un dispositivo "fantasma".

- **b.​ ¿Cómo sabe el kernel que funciones del driver invocar para leer y escribir al dispositivo?**  
  - Lo sabe gracias a la estructura `file_operations` que se pasa en `register_chrdev()`.
  - Cuando se hace un `read()` o `write()` desde user space, el kernel busca el major number del dispositivo, ve cuál file_operations está registrada para ese número, y llama a la función read o write de esa estructura.
- **c.​ ¿Cómo se accede desde el espacio de usuario a los dispositivos en Linux?**  
  - A través de archivos especiales en `/dev`, como `/dev/memory`, `/dev/sda`, etc.
- **d.​ ¿Cómo se asocia el módulo que implementa nuestro driver con el dispositivo?**  
  - El módulo se registra como manejador de un major number.
  - Cuando se crea el archivo en /dev con ese major, el kernel sabe que debe redirigir las operaciones a ese módulo.
    ```C
    register_chrdev(60, "memory", &memory_fops);
    ```
- **e.​ ¿Qué hacen las funciones copy_to_user y copy_from_user?**  
  - En el kernel no se puede acceder directamente a la memoria del espacio de usuario.
  - Estas funciones permiten **copiar datos entre espacio de usuario y espacio kernel**, con seguridad.
    - `copy_from_user(dest_kernel, src_user, size)`:
      - Copia size bytes desde un puntero en espacio de usuario a espacio kernel.
    - `copy_to_user(dest_user, src_kernel, size)`:
      - Copia size bytes desde un puntero en espacio kernel a espacio de usuario.

### 4. 
- **¿Para qué sirve el comando mknod? ¿qué especifican cada uno de sus parámetros?.**
  - El comando mknod se utiliza para crear archivos de dispositivo especiales en el directorio `/dev`. Estos archivos permiten a los programas interactuar con el hardware a través de llamadas estándar como `open`, `read`, `write`, etc.
    ```bash
    mknod <archivo> <tipo> <major> <minor>
    ```

    ```bash
    mknod /dev/memory c 60 0
    ```
  - `/dev/memory`: ruta y nombre del archivo de dispositivo que se crea.
  - `c`: indica que es un dispositivo de carácter (también puede ser `b` para dispositivos de bloque como discos).
  - `60`: major number. Identifica el driver responsable de manejar el dispositivo.
  - `0`: minor number. Se usa para distinguir entre múltiples dispositivos que usa el mismo driver.
  
- **¿Qué son el “major” y el “minor” number? ¿Qué referencian cada uno?.**
  - **Major number:**
    - Es un número que identifica al driver que maneja el dispositivo.
    - Cuando se hace una operación sobre un archivo en /dev, el kernel busca el driver registrado con ese major number y le delega la operación.
  - **Minor number:**
    - Permite distinguir entre varios dispositivos controlados por el mismo driver.

### 7.​
- **a.​ ¿Qué salida tiene el anterior comando?, ¿Porque? (ayuda: siga la ejecución de las funciones memory_read y memory_write y verifique con dmesg)**
  - el comando anterior tiene como resultado imprimir `f`, ya que se lee caracter a caracter **sobreescribiendo** el buffer, pro eso "se queda" con el último `f`
- **b.​ ¿Cuántas invocaciones a memory_write se realizaron?**
  - el comando `memory_write` se invocó 6 veces, uno por cada letra del parametro "abcdef"
- **c.​ ¿Cuál es el efecto del comando anterior? ¿Por qué?**
  - el comando anterior tiene como resultado imprimir `f`, ya que se lee caracter a caracter **sobreescribiendo** el buffer, pro eso "se queda" con el último `f`
- **d.​ Hasta aquí hemos desarrollado un ejemplo de un driver muy simple pero de manera completa, en nuestro caso hemos escrito y leído desde un dispositivo que en este caso es la propia memoria de nuestro equipo.**
  - Se compila como un módulo (.ko)
  - Se carga dinámicamente (insmod)
  - Crea un dispositivo /dev/memory con mknod
  - Permite escribir (aunque solo guarda 1 byte)
  - Permite leer (una única vez ese byte)
- **e.​ En el caso de un driver que lee un dispositivo como puede ser un file system, un dispositivo usb,etc. ¿Qué otros aspectos deberíamos considerar que aquí hemos omitido? ayuda: semáforos, ioctl, inb,outb.**
  - Un driver real necesita control de concurrencia, mecanismos como `ioctl`, y acceso directo a hardware usando instrucciones específicas.
  - **Sincronización**: evitar condiciones de carrera entre procesos concurrentes. Se usan:
    - `semaforos`
    - `spinlocks`
    - `mutex`
  - **Control avanzado del dispositivo**:
    - `ioctl`: permite enviar comandos personalizados desde espacio de usuario.
  - **Acceso a puertos o memoria específica**:
    - `inb()` / `outb()` → para dispositivos que se comunican por puertos I/O.
    - `ioremap()` / `readb()` / `writeb()` → para acceso a memoria mapeada.
