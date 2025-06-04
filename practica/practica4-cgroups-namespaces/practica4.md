# Practica 4
## cgroups & namespaces

## Parte 1: Conceptos teóricos 
### 1. Defina virtualización. Investigue cuál fue la primera implementación que se realizó.
Virtualización es la técnica de ejecutar múltiples sistemas operativos sobre una misma máquina física mediante una capa de software (hipervisor).  
La primera implementación fue el sistema CP-40 / CP/CMS de IBM en los años 60, sobre mainframes IBM System/360 Modelo 67, permitiendo múltiples entornos aislados para distintos usuarios.
### 2. ¿Qué diferencia existe entre virtualización y emulación? 
**Virtualización**  
- La virtualización utiliza el hardware real de la máquina anfitriona (host).
- El sistema operativo invitado (guest) corre directamente sobre el hardware gracias al hipervisor, que coordina el acceso a los recursos físicos.
- Requiere que el guest y el host tengan arquitecturas compatibles (por ejemplo, ambos sean x86-64).
- Tiene alto rendimiento, ya que muchas instrucciones se ejecutan directamente en la CPU.

**Emulación**  
- La emulación simula completamente el hardware de otra arquitectura, permitiendo ejecutar software que fue diseñado para una plataforma distinta (por ejemplo, emular una consola o una arquitectura ARM en una PC x86).
- El emulador traduce instrucción por instrucción, lo que produce una gran sobrecarga y menor rendimiento.
- Es más flexible: puede ejecutarse software de arquitecturas completamente distintas, pero es mucho más lento.
### 3. Investigue el concepto de hypervisor y responda: 
- **a. ¿Qué es un hypervisor?**   
  Un hypervisor es una capa de software que **permite crear, ejecutar y gestionar máquinas virtuales**. Es el componente clave en la virtualización, ya que se encarga de **abstraer y distribuir los recursos físicos (CPU, memoria, disco, red) del host entre múltiples sistemas operativos invitados (guest OS)**.
  Cada VM cree que tiene el control exclusivo del hardware, pero en realidad, **el hypervisor gestiona ese acceso y garantiza el aislamiento entre VMs**.
- **b. ¿Qué beneficios traen los hypervisors? ¿Cómo se clasifican?**  
  - **Aislamiento:** Las VMs no interfieren entre sí; si una falla, no afecta a las demás.
  - **Mejor aprovechamiento del hardware:** Se pueden correr múltiples sistemas en paralelo sobre un mismo servidor físico.
  - **Facilidad para pruebas y desarrollo:** Permite levantar y descartar entornos rápidamente.
  - **Portabilidad:** Las VMs se pueden mover entre hosts físicos.
  - **Escalabilidad y consolidación:** Reducción de costos de infraestructura. 
 
  - **Los hypervisors se clasifican en dos tipos principales:**

    | Tipo       | Nombre alternativo | Características principales                                                                  |
    | ---------- | ------------------ | -------------------------------------------------------------------------------------------- |
    | **Tipo 1** | *Bare metal*       | Se ejecuta **directamente sobre el hardware físico**. Es más eficiente y seguro.             |
    | **Tipo 2** | *Hosted*           | Se ejecuta **sobre un sistema operativo host**. Es más fácil de instalar, pero menos rápido. |


### 4. ¿Qué es la full virtualization? ¿Y la virtualización asistida por hardware? 
La virtualización completa (full virtualization) es una técnica de virtualización en la que **el sistema operativo invitado (guest OS) se ejecuta sin necesidad de ser modificado, como si estuviera corriendo directamente sobre hardware físico**.  
**El hypervisor se encarga de interceptar todas las instrucciones privilegiadas y simular el entorno hardware completo. Esto permite que cualquier sistema operativo pueda correr como VM, sin saber que está virtualizado.**  
Este enfoque, sin embargo, **puede tener un alto costo computacional, ya que requiere técnicas como binary translation** (traducción dinámica de instrucciones) para manejar instrucciones sensibles que no pueden ejecutarse directamente.  
La **virtualización asistida por hardware mejora el rendimiento de la virtualización completa utilizando extensiones del procesador**, como:
- Intel VT-x (Virtualization Technology)
- AMD-V

**Estas extensiones permiten que ciertas operaciones privilegiadas del sistema operativo invitado se ejecuten directamente en la CPU, sin intervención constante del hypervisor. Esto reduce la sobrecarga de la virtualización tradicional, mejora la eficiencia, y permite una full virtualization real sin traducción binaria.**

### 5. ¿Qué implica la técnica binary translation? ¿Y trap-and-emulate? 
**Binary Translation**  
La traducción binaria es una *técnica donde el hypervisor analiza en tiempo real el código binario del sistema operativo invitado, y reemplaza las instrucciones privilegiadas o sensibles por otras que son seguras de ejecutar en un entorno virtualizado*.
- Se realiza dinámicamente y de forma transparente para el guest OS.
- Permite ejecutar sistemas operativos sin modificaciones, incluso en arquitecturas (como x86) que no fueron diseñadas originalmente para virtualización.
- Tiene **alto costo computacional**, especialmente en arquitecturas sin soporte de virtualización por hardware.

**Trap-and-Emulate**  
Es una técnica más clásica y eficiente, que depende del comportamiento del procesador. Funciona así:
- Cuando el guest ejecuta una instrucción privilegiada, el procesador lanza una excepción (trap).
- El hypervisor intercepta esa trampa y emula el efecto de la instrucción, manteniendo el control del hardware.

### 6. Investigue el concepto de paravirtualización y responda: 
- **a. ¿Qué es la paravirtualización?**  
*Es una técnica de virtualización en la que el sistema operativo invitado (guest OS) es modificado explícitamente para poder ejecutarse en una máquina virtual. A diferencia de la virtualización completa, el guest sabe que está virtualizado y colabora con el hypervisor*.  
Este enfoque **evita tener que interceptar y traducir instrucciones privilegiadas** (como en binary translation), porque esas instrucciones son reemplazadas por llamadas explícitas al hypervisor, llamadas hypercalls.
- **b. Mencione algún sistema que implemente paravirtualización.**   
El hipervisor Xen es uno de los ejemplos más conocidos de paravirtualización. En sus primeras versiones, solo soportaba sistemas operativos invitados que estuvieran modificados para funcionar en un entorno paravirtualizado, como versiones adaptadas de Linux y BSD.
- **c. ¿Qué beneficios trae con respecto al resto de los modos de virtualización?**   
  - **Mayor rendimiento:** Al evitar la traducción binaria y la emulación de instrucciones, se reduce la sobrecarga y se mejora la eficiencia.
  - **Simplicidad en el diseño del hypervisor:** Como el guest coopera, el hypervisor no necesita interceptar tantas instrucciones.
  - Aprovechamiento en arquitecturas sin soporte de virtualización por hardware, donde la virtualización completa no es eficiente o ni siquiera viable.

    Sin embargo, requiere que el sistema operativo pueda ser modificado, lo que limita su compatibilidad con ciertos OS propietarios como Windows.
### 7. Investigue sobre containers y responda: 
- **a. ¿Qué son?**  
*Los containers son una forma de virtualización a nivel de sistema operativo. Permiten ejecutar aplicaciones en entornos aislados, donde cada contenedor tiene sus propios procesos, espacio de nombres, sistema de archivos y recursos*.  
A diferencia de una VM, no virtualizan el hardware completo, sino que comparten el kernel del sistema operativo anfitrión.  
Cada contenedor se comporta como un sistema independiente, pero más liviano y eficiente que una máquina virtual.  
- **b. ¿Dependen del hardware subyacente?**  
*No dependen directamente del hardware, ya que los contenedores no emulan hardware ni requieren extensiones de CPU* (como VT-x o AMD-V).
Sin embargo, sí dependen del kernel del sistema operativo host, ya que lo comparten.  
Esto implica:  
  - Todos los contenedores deben ser compatibles con el kernel del host.
  - No se puede ejecutar un sistema operativo completamente distinto dentro de un contenedor (por ejemplo, no se puede correr Windows en un contenedor de Linux).
- **c. ¿Qué lo diferencia por sobre el resto de las tecnologías estudiadas?**  
  - Más eficientes en recursos y velocidad.
  - Menos aislados que una VM (pero suficientemente seguros para muchas aplicaciones).
  - Ideales para entornos de desarrollo, microservicios y despliegues rápidos.

    | Característica       | Máquinas Virtuales       | Contenedores                      |
    | -------------------- | ------------------------ | --------------------------------- |
    | Virtualizan          | Todo el hardware         | Sólo el espacio de usuario del SO |
    | Kernel               | Cada VM tiene el suyo    | Comparten el kernel del host      |
    | Peso / Consumo       | Pesado (GBs)             | Liviano (MBs)                     |
    | Tiempo de arranque   | Lento (segundos/minutos) | Rápido (milisegundos/segundos)    |
    | Rendimiento          | Menor (más overhead)     | Cercano al nativo                 |
    | Aislamiento completo | Sí                       | Parcial (mejorado con namespaces) |

- **d. Investigue qué funcionalidades son necesarias para poder implementar containers.**  
- **Namespace**
- **Cgroups**
- **Union File Systems**
- **Kernel Capabilities:**
  - Permite limitar las capacidades de root dentro de los containers para mejorar la seguridad.
  - Reducción del conjunto de capacidades que un container puede utilizar, limitando las acciones que pueden realizar incluso si se compromete el container.
- **Container Runtime:**
  - Ej. Docker.
- **Image Registry:**
  - Un repositorio donde se almacenan y distribuyen las imágenes de containers.
- **Networking:**
  - Bridge Networks: Conecta containers en un host a través de una red puente.
  - Overlay Networks: Permite que containers en diferentes hosts se comuniquen a través de una red virtual.
  - Host Networks: Los containers comparten la pila de red del host.
- **Security Enhancements:**
  - Seccomp: Permite filtrar las llamadas al sistema que puede hacer un container, restringiendo su interacción con el kernel.
  - AppArmor y SELinux: Sistemas de control de acceso que pueden ser usados para imponer políticas de seguridad sobre containers.
  - Linux Capabilities: Limitación granular de permisos de root dentro de containers.
- **Storage Drivers:**
  - Soporte para diferentes drivers de almacenamiento para gestionar la persistencia de datos de los containers.



## Parte 2: chroot, Control Groups y Namespaces

**<u>Chroot</u>**
### 1.  ¿Qué es el comando chroot? ¿Cuál es su finalidad?
El comando `chroot` (change root) en sistemas Unix/Linux permite cambiar el directorio raíz (`/`) del sistema de archivos para un proceso y sus hijos. Desde ese momento, **ese proceso “cree” que ese nuevo directorio es el sistema de archivos completo**.  
<br>

**Las finalidades principales de `chroot` son:**  
- **Aislamiento:** Limita el acceso de un proceso a una porción del sistema de archivos.(Muy usado para pruebas, tareas administrativas, o entornos reducidos)
- **Recuperación del sistema:** Permite arrancar desde un live CD y usar chroot para acceder al sistema instalado como si estuviera corriendo normalmente. Ejemplo: reinstalar el GRUB o cambiar contraseñas.
- **Entornos controlados para aplicaciones:** Se pueden ejecutar servicios dentro de un chroot para aumentar la seguridad (aunque no es infalible).
- **Simulación de entornos:** Útil para compilar o testear software en un entorno aislado, sin usar máquinas virtuales.
### 2.  Crear un subdirectorio llamado sobash dentro del directorio root. Intente ejecutar el comando chroot /root/sobash. ¿Cuál es el resultado? ¿Por qué se obtiene ese resultado? 
- no encontraba el comando chroot, la variable de entorno $PATH no tenia el directorio `/usr/sbin/` (chroot es una instruccion que deberia ser solo del root)
- `chroot: failed to run command '/bin/bash': No such file or directory`
  - Este error significa que dentro del nuevo entorno raíz que le pasás a chroot (por ejemplo, `/root/sobash/`), no existe el archivo `/bin/bash`, o que sí existe, pero no puede ejecutarse por falta de librerías necesarias.
  - Cuando hacés `chroot /root/sobash/`, el sistema intenta ejecutar `/bin/bash` por defecto.
  - Si no existe o le faltan dependencias, lanza el error.
### 3.  Cree la siguiente jerarquía de directorios dentro de sobash: 
    sobash/   
    ├── bin   
    ├── lib   
    │   └── x86_64-linux-gnu   
    └── lib64   
### 4.  Verifique qué bibliotecas compartidas utiliza el binario /bin/bash usando el comando ldd /bin/bash.¿En qué directorio se encuentra linux-vdso.so.1? ¿Por qué?
```bash
root@so:~/sobash# ldd /bin/bash
        linux-vdso.so.1 (0x00007fa16af95000)
        libtinfo.so.6 => /lib/x86_64-linux-gnu/libtinfo.so.6 (0x00007fa16ae16000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fa16ac35000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fa16af97000)
```

Técnicamente, `linux-vdso.so.1` no existe en disco como un archivo real. Es una biblioteca especial mapeada directamente en memoria por el kernel cuando se crea un proceso. Es decir, no está en el sistema de archivos, no la podés copiar. Su presencia es “virtual” (de ahí vDSO: virtual dynamic shared object).  
La **vDSO (Virtual Dynamic Shared Object)** permite que ciertas llamadas al sistema, como obtener la hora (`gettimeofday`, `clock_gettime`), se hagan sin necesidad de hacer un syscall completo, lo que mejora el rendimiento.
### 5.  Copie en /root/sobash el programa /bin/bash y todas las librerías utilizadas por el programa bash en los directorios correspondientes. Ejecute nuevamente el comando chroot ¿Qué sucede ahora?
Ahora puedo ejecutar el comando `chroot /root/sobash/` sin problemas y se ejecuta un bash 5.2

### 6.  ¿Puede ejecutar los comandos cd "directorio" o echo? ¿Y el comando ls? ¿A qué se debe esto?  
Si se puede ejecutar los comandos `cd` y `echo` porque son comandos built-in, pero no `ls` debido a que es comando externo.

### 7.  ¿Qué muestra el comando pwd? ¿A qué se debe esto?  
El comando `pwd` muestra `/`, como si ahora estuvieramos parados en la carpeta raiz de nuestro fs.

### 8.  Salir del entorno chroot usando exit 


### 9.  Usando el repositorio de la cátedra acceda a los materiales en practica4/02-chroot: 
- **a.  Verifique que tiene instalado busybox en /bin/busybox**  
esto lo verificamos con `ls -l /bin/busybox` o simplemente `busybox`, en caso de que esté instalado estos tendran una salida.

- **b.  Cree un chroot con busybox usando /buildbusyboxroot.sh**   

- **c.  Entre en el chroot**   
entramos con `chroot /home/so/busyboxroot/ /bin/sh`, ya que si por defecto usamos `/bin/bash` no lo va a encontrar en `busybox`

- **d.  Busque el directorio /home/so ¿Qué sucede? ¿Por qué?**   
En este caso no es visible ya que nuestra carpeta raíz estaria dentro de `/home/so/`, en este caso puede ser `home/so/busyboxroot`  

- **e.  Ejecute el comando “ps aux” ¿Qué procesos ve? ¿Por qué (pista: ver el contenido de /proc)?**   
esta vacio, hermosa forma de enseñar

- **f.  Monte /proc con “mount -t proc proc /proc” y vuelva a ejecutar “ps aux” ¿Qué procesos ve? ¿Por qué?**   
  - **Procesos del kernel como:**
    - [kthreadd], [kworker/*], [migration/*]  
  - **Procesos del sistema como:**
    - /sbin/init, systemd, systemd-journald, udevd, dbus-daemon  
  - **Procesos de red como:**
    - dhclient, wpa_supplicant, sshd  
  - **Procesos de usuario como:**
    - bash, code-server, sleep, sh  

- **g.  Acceda a /proc/1/root/home/so ¿Qué sucede?**   
Cuando accedés a `/proc/1/root/home/so`, estás intentando navegar al directorio `/home/so` visto desde el espacio del `proceso 1`, pero ese camino no existe o no es accesible. Por eso el shell no puede obtener la ruta actual y lanza el error `getcwd: No such file or directory`.
  - El error no está en cd, sino en getcwd(), que intenta reconstruir la ruta absoluta.
  - Falló porque /proc/1/root/home/so no es un camino real o completo desde el punto de vista de los inodos del sistema de archivos vistos por el proceso 1.


- **h.  ¿Qué conclusiones puede sacar sobre el nivel de aislamiento provisto por chroot?**   
`chroot` provee un aislamiento superficial del sistema de archivos, útil para entornos controlados o pruebas, pero:
- No es seguro para contención de procesos maliciosos.
- No aísla procesos, usuarios, red ni el kernel.
- Puede ser burlado si no se configura correctamente.

**Lo que chroot sí aísla:**  
- **Aislamiento del sistema de archivos:**
  - Un proceso enjaulado en un chroot no puede acceder a archivos fuera de su raíz virtual —salvo que tenga una forma de escapar.
- **Perspectiva modificada del sistema:**
  - El proceso ve / como su directorio raíz, aunque en realidad eso puede ser, por ejemplo, /var/jail en el sistema real.
- **Buena herramienta para entornos controlados:**
  - Se usa en testing, recuperación del sistema o para ejecutar aplicaciones en entornos mínimos.

**Lo que chroot no aísla:**  
- **No aísla el proceso del resto del sistema operativo:**
  - El proceso aún comparte el mismo kernel y puede ver cosas como /proc, /dev, etc., si no se eliminan o aíslan correctamente.
- **Puede escaparse si tiene privilegios suficientes:**
  - Un proceso con permisos de root puede hacer chroot nuevamente a / y escapar de la jaula. Por eso no es seguro contra root.
- **El acceso a /proc permite observar procesos fuera de la jaula:**
  - Como viste al inspeccionar /proc/1/root/..., podés llegar a explorar caminos fuera del chroot si /proc no está limitado, lo cual rompe el aislamiento de archivos.
- **No aísla red, procesos ni usuarios:**
  - A diferencia de contenedores (como Docker), chroot no impide ver ni comunicarse con otros procesos ni restringe el uso de recursos del sistema (CPU, RAM, etc.).



**<u>Control Groups</u>**
### 1. ¿Dónde se encuentran montados los cgroups? ¿Qué versiones están disponibles?
Los control groups (o cgroups) son una funcionalidad del kernel de Linux que permite limitar, contabilizar y aislar el uso de recursos (CPU, memoria, I/O, etc.) por parte de grupos de procesos.  
**Versión 1 (cgroup v1):**  
- Introducida en el kernel 2.6.24.
- Cada controlador (memory, cpu, blkio, etc.) tiene su propio árbol jerárquico.
- Muy flexible, pero difícil de mantener por su diseño disperso.

**Versión 2 (cgroup v2):**  
- Introducida en el kernel 4.5 (2016) y establecida como predeterminada en muchas distros modernas.
- Usa una jerarquía unificada: todos los controladores están montados en un solo árbol.
- Mejora la consistencia, control y simplicidad.
- Tiene archivos como `cgroup.controllers`, `cgroup.procs`


### 2. ¿Existe algún controlador disponible en cgroups v2? ¿Cómo puede determinarlo? 
Los controladores (también llamados subsystems) son los módulos que permiten aplicar límites o métricas a distintos recursos: CPU, memoria, I/O, etc.  
En **cgroup v2**, todos los controladores se administran desde una jerarquía única (montada en `/sys/fs/cgroup/`) y se activan por cada subgrupo mediante el archivo `/sys/fs/cgroup/cgroup.controllers`
Para determinar si hay algún controlador disponible podemos ejecutar el comando `cat /sys/fs/cgroup/cgroup.controllers`, este archivo lista los controladores que están disponibles para ser activados (por ejemplo: cpu, io, memory, pids, etc.).  
Que estén "disponibles" no significa que estén activos todavía para tu grupo actual. Para activarlos, deben ser escritos en `cgroup.subtree_control`


### 3. Analice qué sucede si se remueve un controlador de cgroups v1 (por ej. Umount /sys/fs/cgroup/rdma). 
En **cgroups v1**, cada controlador está montado en su propio directorio dentro de `/sys/fs/cgroup/`.  
Ejemplos comunes:  
- `/sys/fs/cgroup/cpu/`
- `/sys/fs/cgroup/memory/`
- `/sys/fs/cgroup/pids/`
- `/sys/fs/cgroup/rdma/`

Cada uno gestiona una parte del uso de recursos en el sistema.

El comando `umount /sys/fs/cgroup/rdma` desmonta el controlador de RDMA (Remote Direct Memory Access). Esto implica:  
- Se pierde el acceso a la configuración de cgroups para RDMA.
- Ya no podés ver o modificar límites ni métricas relacionados con RDMA a través de ese path.
- No afecta directamente a los procesos en ejecución, pero:
- Las herramientas que gestionan cgroups pueden fallar al no encontrar ese controlador.
- No se puede asignar nuevos grupos que usen rdma hasta que vuelva a montarse.

### 4. Crear dos cgroups dentro del subsistema cpu llamados cpualta y cpubaja. Controlar que se hayan creado tales directorios y ver si tienen algún contenido 
**mkdir /sys/fs/cgroup/cpu/"nombre_cgroup"** 
```bash
root@so:~# ls /sys/fs/cgroup/cpu/cpubaja
cgroup.clone_children  cpuacct.usage_all          cpuacct.usage_sys   cpu.cfs_quota_us  cpu.stat.local
cgroup.procs           cpuacct.usage_percpu       cpuacct.usage_user  cpu.idle          notify_on_release
cpuacct.stat           cpuacct.usage_percpu_sys   cpu.cfs_burst_us    cpu.shares        tasks
cpuacct.usage          cpuacct.usage_percpu_user  cpu.cfs_period_us   cpu.stat
```

### 5. En base a lo realizado, ¿qué versión de cgroup se está utilizando? 
El sistema está utilizando cgroups v1.  
Esto se confirma por:  
- El uso de subdirectorios separados como /sys/fs/cgroup/cpu/.
- La presencia de archivos de control típicos de cgroup v1.


### 6. Indicar a cada uno de los cgroups creados en el paso anterior el porcentaje máximo de CPU que cada uno puede utilizar. El valor de cpu.shares en cada cgroup es 1024. El cgroup cpualta recibirá el 70 % de CPU y cpubaja el 30 %. 
**echo 717 > /sys/fs/cgroup/cpu/cpualta/cpu.shares** 
**echo 307 > /sys/fs/cgroup/cpu/cpubaja/cpu.shares** 


### 7. Iniciar dos sesiones por ssh a la VM.(Se necesitan dos terminales, por lo cual, también podría ser realizado con dos terminales en un entorno gráfico). Referenciaremos a una terminal como termalta y a la otra, termbaja. 


### 8. Usando el comando taskset, que permite ligar un proceso a un core en particular, se iniciará el siguiente proceso en background. Uno en cada terminal. Observar el PID asignado al proceso que es el valor de la columna 2 de la salida del comando. 
**taskset -c 0 md5sum /dev/urandom &** 

```bash
root@so:~# taskset -c 0 md5sum /dev/urandom &
[1] 2121
```


```bash
root@so:~# taskset -c 0 md5sum /dev/urandom &
[1] 2171
```

### 9. Observar el uso de la CPU por cada uno de los procesos generados (con el comando top en otra terminal). ¿Qué porcentaje de CPU obtiene cada uno aproximadamente? 
2121 - 50/55%  
2171 - 45/50%  

### 10. En cada una de las terminales agregar el proceso generado en el paso anterior a uno de los cgroup (termalta agregarla en el cgroup cpualta, termbaja en cpubaja. El process_pid es el que obtuvieron después de ejecutar el comando taskset) 
**echo "process_pid" > /sys/fs/cgroup/cpu/cpualta/cgroup.procs **


### 11. Desde otra terminal observar cómo se comporta el uso de la CPU. ¿Qué porcentaje de CPU recibe cada uno de los procesos? 
2121 - 70%  
2171 - 30%  

### 12. En termalta, eliminar el job creado (con el comando jobs ven los trabajos, con kill %1 lo eliminan. No se olviden del %.). ¿Qué sucede con el uso de la CPU? 
se termina el proceso 2121(alta) y el proceso 2171(baja) pasa a tener el 100% de cpu

### 13. Finalizar el otro proceso md5sum. 


### 14. En este paso se agregarán a los cgroups creados los PIDs de las terminales (Importante: si se tienen que agregar los PID desde afuera de la terminal ejecute el comando echo $$ dentro de la terminal para conocer el PID a agregar. Se debe agregar el PID del shell ejecutando en la terminal). 
**echo $$ > /sys/fs/cgroup/cpu/cpualta/cgroup.procs (termalta)** 
**echo $$ > /sys/fs/cgroup/cpu/cpubaja/cgroup.procs (termbaja)** 


### 15. Ejecutar nuevamente el comando taskset -c 0 md5sum /dev/urandom & en cada una de las terminales. ¿Qué sucede con el uso de la CPU? ¿Por qué? 
4230 (alta) – 70%  
4180 (baja) – 30%  
Esto se debe a que cada proceso fue creado desde una terminal cuyo shell fue previamente asignado a un cgroup diferente. Al ejecutar:  

- `echo $$ > /sys/fs/cgroup/cpu/cpualta/cgroup.procs` en *termalta*, se agregó el **PID del shell** de esa terminal al cgroup cpualta, por lo tanto el proceso 4230 se ejecuta dentro de ese cgroup.

- `echo $$ > /sys/fs/cgroup/cpu/cpubaja/cgroup.procs` en *termbaja*, se agregó el **PID del shell** de esa terminal al cgroup cpubaja, por lo tanto el proceso 4180 pertenece a ese cgroup.

**Al heredar el cgroup del proceso padre (el shell), cada md5sum corre bajo políticas de CPU distintas, lo que explica la diferencia en el uso del procesador.**


### 16. Si en termbaja ejecuta el comando: taskset -c 0md5sum /dev/urandom & (deben quedar 3 comandos md5 ejecutando a la vez, 2 en el termbaja). ¿Qué sucede con el uso de la CPU? ¿Por qué?
Lo que sucede en este caso es que el 30% que se le daba originalmente a los procesos del cgroup `cpubaja` ahora se dividen 15% entre cada uno de los procesos lanzados en la terminal *termbaja*

**<u>Namespaces</u>** 
### 1. Explique el concepto de namespaces. 
En Linux, *el concepto de namespaces forma parte de las tecnologías de aislamiento del kernel. Su objetivo principal es proporcionar a un proceso (o grupo de procesos) una visión aislada y limitada del sistema operativo, como si fueran los únicos procesos que existen.*  
*Cuando un proceso está en un namespace, solo ve los recursos que están dentro de ese namespace, aunque existan otros recursos en el sistema*. Este aislamiento es clave para la implementación de contenedores, como los que se usan en Docker o LXC.  
**Importancia:**  
- **Seguridad:** los namespaces aíslan procesos, reduciendo el riesgo de que uno acceda a datos o recursos de otro.
- **Escalabilidad:** permiten ejecutar múltiples instancias de un servicio sin conflicto de recursos.
- **Contenedores:** son la base del aislamiento que ofrece Docker y otras tecnologías de virtualización ligera.


### 2. ¿Cuáles son los posibles namespaces disponibles? 
Tipos principales de namespaces: (Cada namespace aísla un aspecto diferente del sistema.) Algunos de los más importantes son:

- `mnt` **(Mount)**: aísla los puntos de montaje, permitiendo que cada namespace tenga su propio sistema de archivos.
- `pid` **(Process ID)**: aísla la numeración de procesos. Los procesos en un namespace pueden tener sus propios PIDs (PID 1, etc.), lo cual permite estructuras similares a init dentro de contenedores.
- `net` **(Network)**: aísla interfaces de red, direcciones IP, tablas de rutas, etc.
- `ipc` **(Interprocess Communication)**: aísla recursos como colas de mensajes, semáforos y memoria compartida.
- `uts` **(UNIX Time-sharing System)**: aísla el hostname y el nombre de dominio del sistema.
- `user`: permite mapear IDs de usuario y grupo diferentes en el namespace respecto del host, permitiendo que un proceso "parezca root" dentro del contenedor sin serlo en el sistema real.
- `cgroup`: permite asignar procesos a distintos controladores de cgroups, controlando recursos como CPU y memoria.

Cuando un proceso se ejecuta con namespaces, el kernel mantiene estructuras separadas para cada uno de los recursos aislados. Estos espacios separados evitan que procesos fuera del namespace interactúen directamente con los que están dentro, y viceversa.

### 3. ¿Cuáles son los namespaces de tipo Net, IPC y UTS una vez que inicie el sistema (los que se iniciaron la ejecutar la VM de la cátedra)? 
```bash
root@so:~# ls -l /proc/1/ns
total 0
lrwxrwxrwx 1 root root 0 may 13 16:05 cgroup -> 'cgroup:[4026531835]'
lrwxrwxrwx 1 root root 0 may 13 17:01 ipc -> 'ipc:[4026531839]'
lrwxrwxrwx 1 root root 0 may 13 17:01 mnt -> 'mnt:[4026531841]'
lrwxrwxrwx 1 root root 0 may 13 17:01 net -> 'net:[4026531840]'
lrwxrwxrwx 1 root root 0 may 13 17:01 pid -> 'pid:[4026531836]'
lrwxrwxrwx 1 root root 0 may 13 17:01 pid_for_children -> 'pid:[4026531836]'
lrwxrwxrwx 1 root root 0 may 13 17:01 time -> 'time:[4026531834]'
lrwxrwxrwx 1 root root 0 may 13 17:01 time_for_children -> 'time:[4026531834]'
lrwxrwxrwx 1 root root 0 may 13 17:01 user -> 'user:[4026531837]'
lrwxrwxrwx 1 root root 0 may 13 17:01 uts -> 'uts:[4026531838]'
```
net -> 'net:[4026531840]'  
ipc -> 'ipc:[4026531839]'  
uts -> 'uts:[4026531838]'  

en este caso los procesos que compartan por ejemplo el inodo `4026531840` (net) podrán ver las mismas tabals de ruteo, interfaces, etc.

### 4. ¿Cuáles son los namespaces del proceso cron? Compare los namespaces net, ipc y uts con los del punto anterior, ¿son iguales o diferentes?  
```bash
root@so:~# ps aux | grep cron
root         631  0.0  0.0   6608  2496 ?        Ss   16:05   0:00 /usr/sbin/cron -f
root        7792  0.0  0.0   6352  2224 pts/3    S+   17:09   0:00 grep cron
root@so:~# ls -l /proc/631/ns/
total 0
lrwxrwxrwx 1 root root 0 may 13 17:09 cgroup -> 'cgroup:[4026531835]'
lrwxrwxrwx 1 root root 0 may 13 17:09 ipc -> 'ipc:[4026531839]'
lrwxrwxrwx 1 root root 0 may 13 17:09 mnt -> 'mnt:[4026531841]'
lrwxrwxrwx 1 root root 0 may 13 17:09 net -> 'net:[4026531840]'
lrwxrwxrwx 1 root root 0 may 13 17:09 pid -> 'pid:[4026531836]'
lrwxrwxrwx 1 root root 0 may 13 17:09 pid_for_children -> 'pid:[4026531836]'
lrwxrwxrwx 1 root root 0 may 13 17:09 time -> 'time:[4026531834]'
lrwxrwxrwx 1 root root 0 may 13 17:09 time_for_children -> 'time:[4026531834]'
lrwxrwxrwx 1 root root 0 may 13 17:09 user -> 'user:[4026531837]'
lrwxrwxrwx 1 root root 0 may 13 17:09 uts -> 'uts:[4026531838]'
```
El proceso cron utiliza los mismos namespaces de red (net), comunicación entre procesos (ipc) y nombre del sistema (uts) que el resto del sistema (como el proceso PID 1).  
Esto indica que cron no está aislado en ningún namespace y forma parte del entorno global del sistema operativo.

### 5. Usando el comando unshare crear un nuevo namespace de tipo UTS. 
- **a.  unshare –uts sh (son dos (- -) guiones juntos antes de uts)**  
Este comando abre un nuevo proceso `sh` en un nuevo UTS namespace. El UTS (Unix Time-Sharing) namespace aísla:
  - El hostname
  - El domain name

Dentro de esta nueva shell, podés usar hostname sin que afecte al sistema principal.
- **b.  ¿Cuál es el nombre del host en el nuevo namespace? (comando hostname)**
La salida va a mostrar el mismo nombre del host del sistema original `so`

- **c.  Ejecutar el comando lsns. ¿Qué puede ver con respecto a los namespace?.** 
El comando `lsns` permite listar todos los namespaces activos del sistema, agrupados por tipo. Cada línea representa un namespace distinto e incluye información como:  
- `NS`: el ID del namespace.
- `TYPE`: el tipo de namespace (por ejemplo, uts, net, ipc, etc.).
- `NPROCS`: la cantidad de procesos que están dentro de ese namespace.
- `PID`: el PID de uno de esos procesos (usualmente el que creó el namespace).
- `USER`: el usuario que ejecuta ese proceso.
- `COMMAND`: el comando que inició ese proceso.

`4026531838 uts     129     1 root   /sbin/init`
Esto indica que 129 procesos comparten un mismo namespace de tipo UTS, iniciado por el proceso PID 1 con el comando /sbin/init. Esto representa el namespace por defecto creado al inicio del sistema.

`4026532239 uts       1   359 systemd-timesync  ├─/lib/systemd/systemd-timesyncd`  
Esto indica que el proceso systemd-timesyncd corre en su propio namespace UTS, aislado del resto. Esto significa que ese proceso puede tener un hostname distinto, lo cual es útil para aislamiento en el sistema.


- **d.  Modificar el nombre del host en el nuevo hostname.** 

- **e.  Abrir otra sesión, ¿cuál es el nombre del host anfitrión?**  
Al abrir una nueva terminal y ejecutar `hostname`, el hostname anfitrion no cambio ya que se realizo dentro del namespace creado

- **f.  Salir del namespace (exit). ¿Qué sucedió con el nombre del host anfitrión?**  
Al ejecutar exit, y luego usar el comando `hostname`, el hostname anfitrion no cambio ya que se realizo dentro del namespace creado


### 6. Usando el comando unshare crear un nuevo namespace de tipo Net.   
- **a.  unshare –pid sh** 

- **b.  ¿Cuál es el PID del proceso sh en el namespace? ¿Y en el host anfitrión?** 
en este caso los pids son diferentes (sin --fork, solo haciendo `unshare --pid sh`)
```bash
so@so:/$ echo $$
1314            # pid de bash

so@so:/$ su root
root@so:/# echo $$
1365            # pid de su

root@so:/# unshare --pid sh
# echo $$       
1528            # pid de sh
```

el proceso `sh` se inicia en el nuevo namespace, al ejecutar `ps -ef` me permite ver todos los procesos ((ve lo mismo que su padre `su`)

- **c.  Ayuda: los PIDs son iguales. Esto se debe a que en el nuevo namespace se sigue viendo el comando ps sigue viendo el /proc del host anfitrión. Para evitar esto (y lograr un comportamiento como los contenedores), ejecutar: unshare --pid --fork  --mount-proc** 

- **d.  En el nuevo namespace ejecutar ps -ef. ¿Qué sucede ahora?** 
LUego de ejecutar `unshare --pid --fork  --mount-proc` en el proceso `sh`, creo un nuevo namespace LO INTERPRETO COMO ANIDACION DE LOS MISMOS ahora tiene el `PID 1` en el nuevo namespace y es un `bash` porque no se aclara en el comando, por esto el bash cuando ejecutamos `ps -ef` vemos solo 2 procesos y no todo lo de su padre
```bash
root@so:/# ps -ef
UID          PID    PPID  C STIME TTY          TIME CMD
root           1       0  0 14:12 pts/0    00:00:00 -bash
root           5       1 28 14:12 pts/0    00:00:00 ps -ef
```

- **e.  Salir del namespace** 

