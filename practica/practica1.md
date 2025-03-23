# Practica 1
## Teoría
### 1.  ¿Qué es GCC?
GCC es una colección de compiladores del proyecto GNU, utilizada para compilar programas en C, C++ y otros lenguajes. Realiza el proceso de conversión de código fuente a ejecutable en varias etapas y es esencial en entornos Unix/Linux, incluyendo la compilación del kernel de Linux.

### 2.  ¿Qué es make y para que se usa? 
make es una herramienta que automatiza la compilación y otras tareas en proyectos de software. Usa un Makefile para definir reglas y dependencias, recompilando solo lo necesario para optimizar el proceso.
**Un Makefile tiene elementos clave:**
- Reglas: Indican cómo generar archivos.
- Dependencias: Archivos que deben estar actualizados.
- Comandos: Acciones para actualizar archivos.
- Variables: Facilitan la configuración.
- Reglas implícitas: Aplican patrones automáticos.
- Reglas especiales (.PHONY): No generan archivos, sino que representan acciones específicas. Representan acciones como clean.
- Comentarios: Se escriben con #.


### 3.  La carpeta /home/so/practica1/ejemplos/01-make de la VM contiene ejemplos de uso de make. Analice los ejemplos, en cada caso ejecute `make` y luego `make run` (es opcional ejecutar el ejemplo 4, el mismo requiere otras dependencias que no vienen preinstaladas en la VM): 
- **a.  Vuelva a ejecutar el comando `make`. ¿Se volvieron a compilar los programas? ¿Por qué?**
  - No hace nada ya que no se modificaron los archivos que Makefile esta indicando como dependencias (archivos que hay que asegurarse esten actualizados)
- **b.  Cambie la fecha de modificación de un archivo con el comando `touch` o editando el archivo y ejecute `make`. ¿Se volvieron a compilar los programas? ¿Por qué?**
  - Por que en este caso la herramienta make revisa y se da cuenta de que los archivos fueron modificados y como son dependencias necesita recompilarlas
- **c.  ¿Por qué “run” es un target “phony”?**
  - .PHONY: run le dice a make que "run" no es un archivo real, sino un nombre para una acción que debe realizarse.
  - Esto evita confusiones y asegura que la receta para ejecutar el programa se ejecute correctamente cada vez que se invoque make run.
  - Es una práctica común usar targets "phony" para acciones que no generan archivos, como clean, run, install, etc., ya que mejora la claridad y la confiabilidad del Makefile.
- **d.  En el ejemplo 2 la regla para el target `dlinkedlist.o` no define cómo generar el target, sin embargo el programa se compila correctamente. ¿Por qué es esto?**
  - El programa se compila correctamente debido a las reglas implícitas de make.
    - **Reglas implícitas de make:**
  make tiene reglas implícitas incorporadas que definen cómo compilar archivos objeto (.o) a partir de archivos fuente C (.c).
  Cuando make encuentra una dependencia de un archivo .o pero no encuentra una regla explícita para construirlo, busca una regla implícita que coincida.
  En este caso, make utiliza la regla implícita para compilar dlinkedlist.o a partir de dlinkedlist.c (si existe) usando el compilador gcc.
  En resumen, aunque no se especifica la regla de compilación, make asume que el objeto dlinkedlist.o se genera a partir de un archivo fuente dlinkedlist.c.
    - **¿Por qué funciona?**  
  make asume que para crear dlinkedlist.o, debe compilar dlinkedlist.c usando el compilador C predeterminado (generalmente gcc).
  Como el archivo dlinkedlist.c existe en el mismo directorio, make puede encontrarlo y compilarlo correctamente.
  Si no existiera el archivo dlinkedlist.c, la compilación fallaría.
  

### 4.  ¿Qué es el kernel de GNU/Linux? ¿Cuáles son sus funciones principales dentro del Sistema Operativo?  
**El kernel de GNU/Linux es el núcleo del sistema operativo y se encarga de:**  
- **Gestionar procesos (creación, planificación, finalización).**
- **Administrar la memoria (RAM, swap, paginación).**
- **Controlar dispositivos mediante drivers.**
- **Manejar el sistema de archivos.**
- **Proveer llamadas al sistema para que los programas interactúen con el hardware.**
- **Administrar redes y protocolos de comunicación.**
- **Garantizar seguridad mediante permisos y control de acceso.**  
  
Es fundamental en Linux, ya que **actúa como intermediario entre el hardware y el software.**


### 5.  Explique brevemente la arquitectura del kernel Linux teniendo en cuenta: tipo de kernel, módulos, portabilidad, etc. 
La arquitectura del kernel de Linux se basa en un modelo monolítico modular, lo que significa que el núcleo ejecuta la mayoría de sus funciones en un único espacio de memoria, pero permite la carga y descarga dinámica de módulos.

**1. Tipo de Kernel: Monolítico Modular**  
Linux es un kernel monolítico, lo que implica que los servicios del sistema (gestión de memoria, procesos, dispositivos, etc.) se ejecutan en modo núcleo para maximizar el rendimiento.
Sin embargo, es modular, lo que permite agregar o quitar módulos del sistema sin necesidad de recompilar el kernel.

**2. Módulos del Kernel**  
Son extensiones que permiten agregar funcionalidades sin modificar el núcleo.
Se pueden cargar y descargar dinámicamente con comandos como insmod y rmmod.
Ejemplos: controladores de dispositivos (usb-storage.ko), sistemas de archivos (ext4.ko), protocolos de red (ipv6.ko).

**3. Portabilidad**  
El kernel de Linux es altamente portable y se ha adaptado a múltiples arquitecturas, desde x86 y ARM hasta supercomputadoras.
Su código es abierto y mantenido por la comunidad, lo que facilita su adaptación a distintos entornos.

**4. Componentes principales**  
- **Gestor de procesos**: Controla la creación y planificación de procesos.
- **Gestor de memoria**: Administra RAM, swap y asignación de páginas.
- **Sistema de archivos virtual (VFS)**: Permite la interacción con distintos sistemas de archivos.
- **Subsistema de red**: Implementa protocolos como TCP/IP.
- **Interfaz de llamadas al sistema**: Permite que las aplicaciones interactúen con el kernel.

### 6.  ¿Cómo se define el versionado de los kernels Linux en la actualidad?  
**El versionado del kernel Linux sigue el formato `X.Y.Z`:**  
- **`X`:** Versión mayor, Cambios estructurales.
  - *Cambia cuando hay modificaciones importantes en la arquitectura del kernel.*  
- **`Y`:** Versión menor, Nuevas funcionalidades.
  - *Se actualiza con nuevas funcionalidades y mejoras.*  
- **`Z`:** Revisión o parche, Correcciones menores.
  - *Se incrementa con correcciones de seguridad o errores.*  

*Existen versiones LTS (soporte extendido) y estándar (actualizaciones frecuentes).*  
*Se lanza un nuevo kernel cada 2-3 meses.*

### 7.  ¿Cuáles son los motivos por los que un usuario/a GNU/Linux puede querer re-compilar el kernel?  
**Los motivos principales para recompilar el kernel son:**  

- **Optimizar rendimiento** eliminando código innecesario.
- **Añadir compatibilidad** con hardware no soportado.
- **Mejorar seguridad** aplicando parches personalizados.
- **Activar características avanzadas** no incluidas en la versión oficial.
- **Reducir el tamaño** en sistemas con pocos recursos.
- **Optimizar para arquitecturas específicas** como ARM.


### 8. ¿Cuáles son las distintas opciones y menús para realizar la configuración de opciones de compilación de un kernel? Cite diferencias, necesidades (paquetes adicionales de software que se pueden requerir), pro y contras de cada una de ellas.  

| Opción	      | Tipo	| Pros	                         | Contras                         |
| --------------- | ------- | ------------------------------ | ------------------------------- |
| make config	  | Consola | Ligero, sin dependencias	     | Tedioso, no permite volver atrás |
| make menuconfig |	ncurses | Interactivo, fácil de navegar	 | Requiere libncurses-dev         |
| make xconfig	  | Gráfica | (Qt)	Interfaz amigable	     | Necesita dependencias Qt        |
| make gconfig	  | Gráfica | (GTK)	Ligero en entornos GTK	 | No ideal para KDE               |
| make oldconfig  |	Consola | Reutiliza configuración previa | No permite revisión completa    |  

Cada opción se usa según el contexto: menuconfig es la más equilibrada para la mayoría de los casos, mientras que oldconfig es ideal para actualizar kernels sin reconfigurar todo.

### 9.  Indique qué tarea realiza cada uno de los siguientes comandos durante la tarea de configuración/compilación del kernel:  
- **a. `make menuconfig`**  
  - Abre una interfaz basada en ncurses (en consola) que permite configurar las opciones del kernel antes de la compilación.
  - Permite seleccionar o desmarcar las características que se quieren incluir o excluir del kernel (por ejemplo, soporte para hardware, sistemas de archivos, módulos, etc.).
- **b. `make clean`**  
  - Limpia los archivos generados de compilaciones previas (objetos, archivos temporales, etc.).
  - Este comando elimina los archivos de objetos (*.o) y otros archivos intermedios para asegurar que la compilación posterior sea completamente desde cero, lo que ayuda a evitar errores derivados de compilaciones previas.
- **c. `make` (investigue la funcionalidad del parámetro -j)**  
  - Compila el kernel usando las configuraciones definidas en el archivo .config.
  - Este comando genera los archivos binarios del kernel (vmlinuz), los módulos y otros componentes necesarios.
  - El parámetro -j permite especificar el número de trabajos paralelos (hilos) que el sistema debe usar durante la compilación.
- **d. `make modules`** (utilizado en antiguos kernels, actualmente no es necesario)  
  - Este comando compila solo los módulos del kernel y no el propio kernel.
  - En versiones más antiguas del kernel, este paso era necesario si se querían compilar y generar los módulos por separado.
  - Actualmente no es necesario en kernels más recientes, ya que el comando make compila tanto el kernel como los módulos simultáneamente.
- **e. `make modules_install`**  
  - Instala los módulos compilados del kernel en el sistema.
  - Copia los módulos a la carpeta /lib/modules/<versión_kernel>, donde serán cargados por el sistema cuando sea necesario (por ejemplo, al iniciar el sistema o al cargar un módulo manualmente).
  - Asegura que los módulos recién compilados estén disponibles para el sistema.
- **f. `make install`**  
  - Instala el kernel compilado y otros archivos relacionados en el sistema.
  - Copia el kernel binario (vmlinuz) y los archivos de configuración al directorio de arranque (/boot).
  - También instala los módulos del kernel en el directorio /lib/modules.
  - Modifica el gestor de arranque (como GRUB) para agregar una entrada para el nuevo kernel.
  - Este paso es fundamental para que el sistema arranque con el nuevo kernel después de reiniciar.


### 10. Una vez que el kernel fue compilado, ¿dónde queda ubicada su imagen? ¿dónde debería ser reubicada? ¿Existe algún comando que realice esta copia en forma automática?  
Cuando compila el kernel, la imagen del  resultante se ubica típicamente en el directorio `arch/<arquitectura>/boot/`. Por ejemplo, en sistemas x86_64, la imagen del kernel se encuentra en `arch/x86_64/boot/bzImage` o `arch/x86_64/boot/vmlinuz`.  
La imagen del kernel debe ser reubicada en el directorio `/boot/` del sistema de archivos raíz. Este directorio es donde el cargador de arranque (como GRUB) busca el kernel para cargar al inicio del sistema.  
<br>
**El comando que realiza esta copia automaticamente es `make install`:** 
- Copia la imagen del kernel al directorio /boot, cambiando su nombre a algo como vmlinuz-<versión>.
- Copia los módulos del kernel a /lib/modules/<versión>.
- Actualiza el gestor de arranque (como GRUB) para que incluya la nueva entrada de kernel.

### 11. ¿A qué hace referencia el archivo initramfs? ¿Cuál es su funcionalidad? ¿Bajo qué condiciones puede no ser necesario?  
| Aspecto	               | Descripción                                                                                                                     |
| ------------------------ | ------------------------------------------------------------------------------------------------------------------------------- |
| ¿Qué es?	               | Es un sistema de archivos comprimido que se carga en la RAM durante el arranque.                                                |
| Funcionalidad	           | Contiene herramientas y controladores para iniciar el sistema, montar el sistema de archivos raíz, y más.                       |
| ¿Cuándo no es necesario? | En sistemas simples, sin RAID, LVM, cifrado, o hardware especial, el kernel puede montar el sistema de archivos raíz sin ayuda. |

### 12. ¿Cuál es la razón por la que una vez compilado el nuevo kernel, es necesario reconfigurar el gestor de arranque que tengamos instalado?  
| Razón	                                     | Descripción                                                                                                            |
| ------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------- |
| Actualización del kernel	                 | El nuevo kernel puede estar en una ubicación diferente o tener un nombre distinto.                                     |
| Cambio de versión	                         | El gestor de arranque debe actualizarse para reconocer la nueva versión del kernel y sus parámetros.                   |
| Nuevas entradas de arranque	             | Se deben crear nuevas entradas para el nuevo kernel y posiblemente también para versiones anteriores.                  |
| Parámetros de arranque	                 | El nuevo kernel podría requerir configuraciones o parámetros diferentes que deben reflejarse en el gestor de arranque. |
| Actualización del archivo de configuración | Es necesario actualizar el archivo de configuración del gestor de arranque para que pueda detectar el nuevo kernel.    |

### 13. ¿Qué es un módulo del kernel? ¿Cuáles son los comandos principales para el manejo de módulos del kernel?  
Un módulo del kernel es un componente del sistema operativo que se puede cargar o descargar dinámicamente para extender las funcionalidades del kernel sin necesidad de recompilarlo o reiniciar el sistema. Los módulos permiten agregar soporte para nuevos dispositivos, sistemas de archivos, o incluso protocolos de red, sin modificar el núcleo principal del sistema operativo.

| Comando                      | Descripción                                                    |
| ---------------------------- | -------------------------------------------------------------- |
| lsmod	                       | Muestra los módulos actualmente cargados en el kernel.         |
| modprobe <nombre_del_módulo> | Carga o descarga (flag -r) módulos del kernel automáticamente. |
| insmod <ruta_al_módulo.ko>   | 	Carga un módulo del kernel, pero sin manejar dependencias.  |
| rmmod <nombre_del_módulo>    |	Elimina un módulo cargado del kernel.                       |
| modinfo <nombre_del_módulo>  |	Muestra información detallada sobre un módulo.              |

### 14. ¿Qué es un parche del kernel? ¿Cuáles son las razones principales por las cuáles se deberían aplicar parches en el kernel? ¿A través de qué comando se realiza la aplicación de parches en el kernel?  
Un parche del kernel es un conjunto de cambios, correcciones o mejoras al código fuente del kernel, que se aplican para solucionar problemas específicos, agregar nuevas funcionalidades o mejorar la seguridad del sistema operativo. Los parches pueden ser proporcionados por los desarrolladores del kernel o por otros contribuyentes y, por lo general, se distribuyen en forma de archivos diff que contienen las diferencias entre las versiones anterior y nueva del código.  

Razones principales para aplicar parches en el kernel  

| Razón                      | Descripción                                                                     |
| -------------------------- | ------------------------------------------------------------------------------- |
| Corrección de errores      | 	Solucionar bugs en el código del kernel.                                       |
| Mejoras de rendimiento     | Optimización del rendimiento y eficiencia del sistema operativo.                |
| Seguridad                  | Corregir vulnerabilidades de seguridad que pueden ser explotadas por atacantes. |
| Compatibilidad de hardware | Agregar soporte para nuevos dispositivos y tecnologías.                         |
| Nuevas funcionalidades     | Introducir nuevas capacidades o mejorar las existentes en el kernel.            |

|Comando para aplicar el parche | Descripción                                                                                             |
| ----------------------------- | ------------------------------------------------------------------------------------------------------- |
|patch	                        | Se usa para aplicar un parche al código fuente del kernel. Ejemplo: patch -p1 < nombre_del_parche.patch |
|Compilación y reinstalación	| Después de aplicar el parche, es necesario recompilar el kernel usando make, make modules, make install |


### 15. Investigue la característica Energy-aware Scheduling incorporada en el kernel 5.0 y explique brevemente con sus palabras:  
- **a.  ¿Qué característica principal tiene un procesador ARM big.LITTLE?**  
  Un procesador ARM big.LITTLE combina dos tipos de núcleos en la misma arquitectura y tiene la capacidad de cambiar dinámicamente entre núcleos grandes (para alto rendimiento) y pequeños (para eficiencia energética) según las demandas del sistema:
  - **Núcleos grandes (big):** Están diseñados para ofrecer un alto rendimiento, pero con un mayor consumo de energía. Son ideales para tareas que requieren más potencia de procesamiento.
  - **Núcleos pequeños (LITTLE):** Son eficientes en términos de energía y están diseñados para tareas de bajo consumo, adecuándose a procesos que no necesitan tanta potencia de CPU.
- **b.  En un procesador ARM big.LITTLE y con esta característica habilitada. Cuando se despierta un proceso ¿a qué procesador lo asigna el scheduler?**  
  - Cuando se habilita Energy-aware Scheduling (EAS), el scheduler de Linux toma decisiones basadas en el consumo de energía y en las necesidades de rendimiento del proceso.
  - En general, cuando se despierta un proceso que no tiene una alta demanda de rendimiento, el scheduler tiende a asignarlo a los núcleos LITTLE, para minimizar el consumo de energía.
  - Sin embargo, si el proceso requiere un mayor rendimiento (por ejemplo, si es un proceso de alta carga), el scheduler lo puede asignar a los núcleos big, aunque esto aumentará el consumo energético.
- **c.  ¿A qué tipo de dispositivos opinás que beneficia más esta característica? Ver https://docs.kernel.org/scheduler/sched-energy.html**  
  - Beneficia principalmente a dispositivos móviles (smartphones, tablets) y dispositivos IoT que requieren eficiencia energética.     
### 16. Investigue la system call memfd_secret() incorporada en el kernel 5.14 y explique brevemente con sus palabras 
- **a.  ¿Cuál es su propósito?**  
  Permitir la creación de regiones de memoria anónimas y protegidas, que no pueden ser accedidas por otros procesos ni el kernel.
- **b.  ¿Para qué puede ser utilizada?**  
  Para almacenar información sensible como claves criptográficas, datos de autenticación, y otros tokens de seguridad.
- **c.  ¿El kernel puede acceder al contenido de regiones de memoria creadas con esta system call?**  
  No, el kernel no puede acceder a los contenidos de las regiones de memoria creadas con memfd_secret(), lo que mejora la seguridad.