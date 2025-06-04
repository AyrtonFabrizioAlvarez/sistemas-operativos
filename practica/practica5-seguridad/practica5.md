# Practica 5 Seguridad

# ​A - Introducción

## 1.​ Defina política y mecanismo.  
**<u>Política</u>**: define qué está permitido o prohibido. *Por ejemplo, una política podría ser: “Sólo los usuarios del grupo admin pueden ejecutar un archivo”.*  
**<u>Mecanismo</u>**: define cómo se implementan esas reglas. *Por ejemplo, el sistema de permisos de archivos en UNIX (rwx) es un mecanismo que permite hacer cumplir la política anterior.*  

## 2.​ Defina objeto, dominio y right.  
**Estos conceptos se utilizan en el control de acceso**  
**<u>Objeto</u>**: es un recurso del sistema al que se quiera controlar el acceso. *Ejemplos: archivos, sockets, memoria, impresoras.*  
**<u>Dominio</u>**: es el contexto de ejecución o autoridad de un sujeto (típicamente un proceso). Define qué operaciones están permitidas para ese proceso en un momento dado. *Por ejemplo: GID(grupo), UID(usuario)*
**<u>Right (derecho)</u>**: es una acción específica que puede realizarse sobre un objeto desde un dominio. *Por ejemplo: read, write, execute.*

## 3.​ Defina POLA (Principle of least authority).  
POLA (Principle of Least Authority) establece que todo componente (usuario, proceso, módulo) debe tener sólo los privilegios estrictamente necesarios para realizar su tarea, y nada más.  

**<u>Este principio reduce</u>**:
- La superficie de ataque *(menos privilegios = menos posibilidades de daño).*
- El impacto de errores o exploits *(si un proceso comprometido tiene pocos permisos, las consecuencias son limitadas)*.
- El riesgo de abuso intencional o accidental.

## 4.​ ¿Qué valores definen el dominio en UNIX?  
En UNIX, el **dominio de protección de un proceso** se define principalmente por:  

**<u>UID (User ID)</u>**: identifica al usuario que ejecuta el proceso. Determina los permisos de acceso según el dueño de los archivos y recursos.
**<u>GID (Group ID)</u>**: identifica al grupo primario del proceso, relevante para archivos compartidos entre usuarios del mismo grupo.
**<u>Lista de grupos secundarios</u>**: también puede influir en los permisos.

Estos valores determinan qué recursos puede acceder un proceso y con qué derechos, utilizando los mecanismos de control de acceso del sistema de archivos *(rwx para owner, group, others)*.

## 5.​ ¿Qué es ASLR (Address Space Layout Randomization)? ¿Linux provee ASLR para los procesos de usuario? ¿Y para el kernel?  
ASLR (Address Space Layout Randomization) es una técnica de seguridad que consiste en aleatorizar las direcciones de memoria *(stack, heap, librerías, etc.)* de un proceso cada vez que se ejecuta.  
Esto dificulta los ataques como buffer overflows, ya que los atacantes no pueden predecir con facilidad dónde están ubicadas instrucciones críticas *(como funciones del sistema o cadenas específicas)*.  

**<u>En Linux</u>**:  
- Sí, ASLR está implementado y disponible para los procesos de usuario desde el kernel 2.6.12.
- También existe ASLR para el kernel, aunque más limitado y complejo, porque ciertas estructuras deben residir en direcciones fijas. A partir del kernel 4.12 se incluye Kernel Address Space Layout Randomization (KASLR).

## 6.​ ¿Cómo se activa/desactiva ASRL para todos los procesos de usuario en Linux?  
```bash
/proc/sys/kernel/randomize_va_space
```
**<u>Este archivo acepta tres valores</u>**:
- 0: **Desactivado** — sin aleatorización.
- 1: **Parcial** — aleatoriza el stack, pero no el heap ni librerías.
- 2: **Completo** — aleatorización total *(recomendado para seguridad)*.




# ​B - Ejercicio introductorio: Buffer Overflow simple

## 5.​ Después de realizar la explotación, reflexiona sobre las siguientes preguntas:
**a.​ ¿Por qué el uso de gets() es peligroso?**  
La función `gets()` es peligrosa porque no realiza ninguna verificación del tamaño del buffer donde escribe.
- Lee entrada hasta encontrar un salto de línea (\n), sin importar cuántos caracteres ingresó el usuario.
- Si el buffer es más chico que la entrada, `gets()` escribirá fuera de los límites del array, causando un desbordamiento de pila (stack buffer overflow).
 
Esto permite que un atacante sobrescriba variables adyacentes, incluyendo direcciones de retorno, punteros o flags de seguridad, lo cual puede llevar a:
- Ejecución de código arbitrario
- Acceso a funciones privilegiadas (como en tu caso)
- Toma de control del proceso

**b.​ ¿Cómo se puede prevenir este tipo de vulnerabilidad?**  
- Evitar funciones inseguras: No usar `gets()`, `strcpy()`, `sprintf()`, etc. En su lugar, usar:
  - `fgets()` *(limita la cantidad de caracteres leídos)*
  - `snprintf()` *(verifica el tamaño del buffer)*
  - `strncpy()` *(versión segura de `strcpy()`)*
- validar la entrada del usuario: **Nunca asumir que la entrada será corta o bien formada.**
- Usar herramientas de análisis estático: como `valgrind`, `cppcheck` o `linters` que detectan vulnerabilidades.
- Programar con principios de defensa en profundidad, como **separación de privilegios o restricciones por capacidades**.
- **Evitar trabajar directamente con punteros sin saber sus límites.**

**c.​ ¿Qué medidas de seguridad ofrecen los compiladores modernos para evitar estas vulnerabilidades?**  
Los compiladores modernos y los sistemas operativos incorporan varias defensas:

**<u>Stack canaries</u>**: se coloca un valor secreto (canario) entre las variables locales y la dirección de retorno. Si se modifica, el programa detecta el desbordamiento y se aborta.
**<u>ASLR (Address Space Layout Randomization)</u>**: aleatoriza las direcciones de memoria (heap, stack, etc.) para que los atacantes no puedan predecirlas.
**<u>No-eXecute (NX) / DEP</u>**: marca regiones de memoria como no ejecutables, por lo que incluso si un atacante inyecta código en el stack, no puede ejecutarlo.
**<u>Fortify Source</u>**: técnica de compilación (-D_FORTIFY_SOURCE=2) que introduce chequeos de límites adicionales en tiempo de ejecución para funciones peligrosas.
**<u>Relocation Read-Only (RELRO)</u>**: protección de las tablas de direcciones usadas por el sistema dinámico de enlace (GOT, PLT).
**<u>PIE (Position Independent Executable)</u>**: permite que el ejecutable sea cargado en direcciones aleatorias, complementando ASLR.

Muchos de estos se activan con flags como `-fstack-protector`, `-Wformat`, `-Werror`, `-D_FORTIFY_SOURCE`, etc.




# ​C - Ejercicio: Buffer Overflow reemplazando dirección de retorno
## 6.​ Suponiendo que el compilador no agregó ningún padding en el stack tenemos los siguientes datos:
a.​ El stack crece hacia abajo.  
b.​ Si estamos compilando en x86_64 los punteros ocupan 8 bytes.  
c.​ x86_64 es little endian.  
d.​ Primero se apiló la dirección de retorno (una dirección dentro de la función main()). Ocupa 8 bytes.  
e.​ Luego se apiló la vieja base de la pila (rbp). Ocupa 8 bytes.  
f.​ password ocupa 16 bytes.  
Calcule cuántos bytes de relleno necesita para pisar la dirección de retorno.  
Para pisar la direccion de retorno deberiamos rellenar los 16bytes de password + 8bytes de rbp + 1 byte para pisar la direccion  

## 11.​Conteste:
**a.​ ¿Qué efecto tiene setear el bit setuid en un programa si el propietario del archivo es root? ¿Qué efecto tiene si el usuario es por ejemplo nobody?**  
El bit `setuid` hace que un programa se ejecute con el `UID` del dueño del archivo, no del usuario que lo lanza. Si el dueño es root, el programa se ejecuta como root.

**b.​ Compare el resultado del siguiente comando con la dirección de memoria de privileged_fn(). ¿Qué puede notar respecto a los octetos? ¿A qué se debe esto?** `python payload_pointer.py --padding <padding> --pointer <pointer> | hd`  
```bash
so@so:~/practica5/practica5$ python3 payload_pointer.py --padding 24 --pointer 0x5555555551c9 | hd
00000000  30 31 32 33 34 35 36 37  38 39 61 62 63 64 65 66  |0123456789abcdef|
00000010  67 68 69 6a 6b 6c 6d 6e  c9 51 55 55 55 55 00 00  |ghijklmn.QUUUU..|
00000020  0a 0a                                             |..|
00000022
```
La dirección se ve como c9 51 55 55 55 55 porque el sistema usa little endian: los bytes se almacenan desde el menos al más significativo.

**c.​ ¿Cómo ASLR ayuda a evitar este tipo de ataques en un escenario real donde el programa no imprime en pantalla el puntero de la función objetivo?**  
ASLR dificulta estos ataques al aleatorizar la memoria; sin direcciones visibles, el atacante no puede preparar un puntero válido.

**d.​ ¿Cómo podría evitar este tipo de ataques en un módulo del kernel de Linux? ¿Qué mecanismo debería estar habilitado?**  
En el kernel, para evitar ataques como el desbordamiento de buffer y control de ejecución, existen varios mecanismos. Uno importante es:

**SMEP – Supervisor Mode Execution Prevention**
- Evita que el kernel ejecute código ubicado en memoria del espacio de usuario.
- Si un atacante intenta redirigir el flujo del kernel a una dirección controlada en user space, el CPU lo bloquea.

También son relevantes:
- **SMAP (Supervisor Mode Access Prevention)**: impide leer datos del user space desde el kernel, salvo excepciones explícitas.
- **KASLR (Kernel Address Space Layout Randomization)**: como ASLR pero para el kernel, aleatoriza las direcciones del propio código kernel.




# D - Ejercicio SystemD

## 1.​ Investigue los comandos:
**a.​ systemctl enable**  
`systemctl enable` permite que un servicio arranque automáticamente con el sistema. Lo hace creando enlaces simbólicos (symlinks)
```bash
sudo systemctl enable nombre-del-servicio
```

**b.​ systemctl disable**  
`systemctl disable` impide que un servicio se inicie al arrancar el sistema. Lo hace eliminando enlaces simbólicos (symlinks) creados con `enable`
```bash
sudo systemctl disable nombre-del-servicio
```

**c.​ systemctl daemon-reload**  
`systemctl daemon-reload` actualiza SystemD con los cambios en los archivos de configuración.
```bash
sudo systemctl daemon-reload
```

**d.​ systemctl start**  
`systemctl start` arranca un servicio inmediatamente, pero no lo habilita para que arranque con el sistema.
```bash
sudo systemctl start nombre-del-servicio
```

**e.​ systemctl stop**  
`systemctl stop` detiene un servicio que está corriendo, este efecto es temporal (no afecta al arranque automático).
```bash
sudo systemctl stop nombre-del-servicio
```

**f.​ systemctl status**  
`systemctl status` muestra el estado actual de una unidad: si está activa, errores recientes, PID, logs, etc. Muy útil para diagnosticar servicios.
```bash
sudo systemctl status nombre-del-servicio
```

**g.​ systemd-cgls**  
`system-cgls` muestra el árbol jerárquico de cgroups (control groups) utilizados por SystemD para organizar los procesos. Permite ver cómo están agrupados y controlados los procesos del sistema.
```bash
systemd-cgls
```

**h.​ journalctl -u [unit]**  
`journalctl -u [unit]` muestra los mensajes del journal (sistema de logs) filtrados por una unidad específica. Muy útil para revisar qué hizo un servicio, errores, o actividad cronológica.
```bash
journalctl -u ssh.service
journalctl -u ssh.service -f # en tiempo real
```

## 2.​ Investigue las siguientes opciones que se pueden configurar en una unit service de systemd:
**a.​ IPAddressDeny e IPAddressAllow**  
Estas directivas controlan el acceso a direcciones IP desde el proceso del servicio. Funcionan en conjunto: primero se niega (deny) todo, luego se permite (allow) explícitamente. 
- `IPAddressDeny=0.0.0.0/0` → bloquea todo el acceso a la red.
- `IPAddressAllow=192.168.1.0/24` → permite el acceso solo a esa red. *(Se puede usar más de una línea para permitir múltiples rangos.)*
 
Requieren que el servicio se ejecute en su propio namespace de red (se activa automáticamente si usás estas directivas).  

**b.​ User y Group**  
Especifican el usuario y grupo UNIX bajo el cual se ejecutará el servicio.  
- `User=www-data`
- `Group=www-data`

Por defecto, los servicios se ejecutan como root, lo cual es riesgoso. Cambiar esto es una buena práctica de seguridad.

**c.​ ProtectHome**  
Restringe el acceso del servicio a los directorios `/home`, `/root` y `/run/user`. Se usa para proteger archivos personales del sistema o de usuarios.  
- `ProtectHome=yes`: oculta completamente esos directorios.
- `ProtectHome=read-only`: los hace de solo lectura.
- `ProtectHome=no`: no aplica restricciones.

**d.​ PrivateTmp**  
Crea un directorio `/tmp` y `/var/tmp` privados para el servicio, separados del resto del sistema.  
- Actúa como un namespace de filesystem temporal.
- `PrivateTmp=yes` → activa el aislamiento.
- Evita que procesos accedan a archivos temporales de otros servicios o usuarios.

**e.​ ProtectProc**  
Controla qué tan accesible es el contenido de /proc para el servicio.  
- `ProtectProc=no` → sin restricción.
- `ProtectProc=ptraceable` → solo procesos trazables son visibles.
- `ProtectProc=invisible` → oculta casi todo /proc.
- `/proc` contiene información sensible del sistema y de otros procesos.

**f.​ MemoryAccounting, MemoryHigh y MemoryMax**  
Permiten aplicar limitaciones y seguimiento del uso de memoria RAM del servicio (vía cgroups). Ideal para contener servicios que consumen mucha memoria.  
- `MemoryAccounting=yes` → habilita el seguimiento del uso de memoria.
- `MemoryHigh=100M` → límite blando, se intenta mantener por debajo.
- `MemoryMax=200M` → límite duro, si se excede puede matar el proceso.




