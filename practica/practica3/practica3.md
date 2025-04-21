# Practica 2
## Threads
### 1.  ¿Cuál es la diferencia fundamental entre un proceso y un thread?  
Un proceso es una unidad de ejecución con memoria propia y aislada; un thread es una unidad de ejecución dentro de un proceso, que comparte memoria con otros threads del mismo proceso.

### 2.  ¿Qué son los User-Level Threads (ULT) y cómo se diferencian de los Kernel-Level Threads (KLT)? 
| Característica             | ULT                               | KLT
| -------------------------- | --------------------------------- | ------------------------------
| Visibilidad para el kernel | No                                | Sí
| Velocidad de gestión       | Rápida (user space)               | Más lenta (requiere llamadas al SO)
| Planificación              | Biblioteca en espacio de usuario  | Kernel
| Multiprocesamiento real    | No (solo 1 hilo ejecuta a la vez) | Sí (varios hilos pueden correr a la vez)
| Bloqueo por syscall        | Bloquea todo el proceso           | Solo bloquea al hilo

### 3.  ¿Quién  es  responsable  de  la  planificación  de  los  ULT?  ¿y  los  KLT?  ¿Cómo  afecta esto al rendimiento en sistemas con múltiples núcleos?
**La planificación de los `ULT` está a cargo de una biblioteca de espacio de usuario** (como puede ser una implementación de `pthreads` o una biblioteca personalizada). El sistema operativo no sabe que esos hilos existen: desde su punto de vista, es solo un proceso.  
En cambio, **los `KLT` son planificados por el kernel del sistema operativo**. El kernel tiene conocimiento de cada hilo, los incluye en su planificador global y puede decidir cuál ejecutar, pausarlos, y moverlos entre CPUs si es necesario.

**<u>Impacto en sistemas con múltiples núcleos:</u>**
- Los `KLT` se benefician totalmente de arquitecturas con múltiples núcleos, ya que el kernel puede ejecutar diferentes hilos del mismo proceso en distintos núcleos al mismo tiempo (paralelismo verdadero).
- Los `ULT`, al no ser visibles para el kernel, no pueden aprovechar múltiples núcleos por sí solos. Solo un hilo del proceso se ejecutará a la vez, incluso si hay varios disponibles. Esto significa que hay concurrencia (pueden alternarse), pero no paralelismo real (ejecución simultánea).

Sin embargo, en modelos híbridos (como M:N, donde múltiples ULT se asignan a múltiples KLT), es posible combinar ventajas: la biblioteca de usuario gestiona la lógica interna, y el kernel permite el uso de múltiples núcleos.

### 4.  ¿Cómo maneja el sistema operativo los KLT y en qué se diferencian de los procesos? 
**<u>El sistema operativo trata a cada `KLT` como una entidad independiente de planificación:**</u>
- Cada KLT tiene su propia entrada en la tabla de procesos (o tabla de hilos) del kernel.
- El kernel le asigna a cada KLT un contexto de ejecución (registro, contador de programa, pila, etc.).
- El planificador del SO puede interrumpir, suspender o migrar individualmente un KLT de una CPU a otra.
- Cuando un hilo bloquea (por ejemplo, por I/O), los otros hilos del mismo proceso pueden seguir ejecutándose.

### 5.  ¿Qué ventajas tienen los KLT sobre los ULT? ¿Cuáles son sus desventajas?
**<u>Ventajas de los KLT sobre los ULT:</u>**
- **Paralelismo real:**
  - Como los KLT son conocidos por el kernel, pueden ejecutarse simultáneamente en múltiples núcleos. Esto permite aprovechar procesadores multinúcleo, algo que los ULT no pueden hacer por sí solos.
- **Bloqueo no afecta a otros hilos:**
  - Si un KLT realiza una llamada bloqueante (por ejemplo, espera por I/O), los demás hilos del mismo proceso pueden seguir ejecutándose. En cambio, con ULT, un hilo bloqueado puede detener a todo el proceso.
- **Planificación independiente:**
  - El kernel puede asignar prioridad, suspender o migrar hilos individualmente, lo que permite un control más fino sobre el rendimiento del sistema.
- **Mejor integración con el sistema operativo:**
  - Los KLT pueden utilizar directamente recursos del sistema (como llamadas a la API del SO) y pueden beneficiarse de servicios como profiling, debugging y trazas más detalladas.

**<u>Desventajas de los KLT frente a los ULT:</u>**
- **Mayor overhead:**
  - Crear, destruir o conmutar entre KLTs es más costoso, porque implica llamadas al sistema (modo kernel), lo cual consume tiempo y recursos.
- **Escalabilidad limitada para grandes cantidades:**
  - Si se crean miles de KLTs, se sobrecarga al planificador del kernel, que debe gestionarlos todos, afectando el rendimiento global.
- **Menor control del programador:**
  - Con ULT, el programador o biblioteca puede definir su propia política de planificación. Con KLT, esto depende del kernel y puede no ser óptimo para ciertos tipos de aplicaciones.
- **Sin flexibilidad para planificación personalizada:**
  - En los ULT, el desarrollador puede aplicar técnicas específicas de planificación (ej: cooperativa), lo cual no es posible directamente en KLT.
### 6.  Qué retornan las siguientes funciones: 
- **a.  getpid()**
  - Retorna el `PID` (Process ID) del proceso que la llama.
  - **Ejemplo:** Si el proceso tiene PID 1357, getpid() devuelve 1357.
- **b.  getppid()**
  - Retorna el `PID` del proceso padre del proceso actual. 
  - **Ejemplo:** Si tu proceso fue creado por el shell (bash) con PID 4321, entonces getppid() devuelve 4321.
- **c.  gettid()** 
  - Retorna el `TID` (Thread ID) del hilo actual, como lo ve el kernel de Linux.
  - **Ejemplo:** En Linux, dos hilos de un mismo proceso pueden tener el mismo PID (getpid()), pero diferente gettid().
- **d.  pthread_self()** 
  - Retorna un identificador del hilo actual de tipo `pthread_t`, definido por la biblioteca POSIX Threads.
  - Este identificador puede ser usado para comparar hilos (`pthread_equal()`), pero no es necesariamente un entero ni el mismo valor que gettid().
  - **Ejemplo:** Retorna algo como 0x7fbd880a4700.
- **e.  pth_self()** **YA NO SUELE USARSE**
  - Esta función pertenece a la biblioteca GNU Portable Threads (GNU Pth), NO es estándar ni parte de POSIX.
  - Retorna el identificador del hilo actual dentro del sistema de hilos user-level que maneja pth.
  - Se usa en entornos donde los hilos son implementados en espacio de usuario, no por el kernel.
### 7.  ¿Qué  mecanismos  de  sincronización se pueden usar? ¿Es necesario usar mecanismos de sincronización si se usan ULT?
- Se pueden usar **mutexes, semáforos, monitores, variables de condición, spinlocks, entre otros.**
- **Sí es necesario usar sincronización con ULT**, ya que comparten memoria y pueden provocar condiciones de carrera, aunque estén en espacio de usuario.
### 8.  Procesos 
- **a.  ¿Qué utilidad tiene ejecutar fork() sin ejecutar exec()**
  - Permite crear un proceso hijo que es una copia exacta del padre, incluyendo código, datos y pila.
  - Se usa cuando el proceso hijo va a continuar ejecutando el mismo programa, quizás con alguna variación (por ejemplo, en procesos concurrentes o servidores con múltiples conexiones).
  - También es útil para crear procesos hijos que realizan una parte específica del trabajo sin reemplazar su código.
  - **Ejemplo:** en un servidor, el proceso padre puede hacer fork() y dejar que el hijo atienda al cliente, sin usar exec().
- **b.  ¿Qué utilidad tiene ejecutar fork() + exec()?**
  - Se usa para crear un nuevo proceso y cargar en él otro programa diferente.
  - `fork()` crea un proceso hijo, y `exec()` reemplaza su imagen con la de otro programa.
  - Es el modelo clásico de creación de procesos en UNIX: primero clono, después transformo.
- **c.  ¿Cuál de las 2 asigna un nuevo PID fork() o exec()?** 
  - `fork()` es quien asigna un nuevo PID.
  - `exec()` no crea un nuevo proceso, solo reemplaza el código del proceso actual.
- **d.  ¿Qué implica el uso de Copy-On-Write (COW) cuando se hace fork()?** 
  - Significa que el sistema operativo no copia inmediatamente la memoria del proceso padre al hijo.
  - En su lugar, ambos procesos comparten las mismas páginas de memoria marcadas como "solo lectura".
  - Si uno de los dos modifica una página, entonces el sistema copia esa página (de ahí el nombre “copy-on-write”).
  - **Ventajas:**
    - Ahorra tiempo y memoria si el hijo va a hacer un exec() enseguida.
    - Mejora la eficiencia de fork().
- **e.  ¿Qué consecuencias tiene no hacer wait() sobre un proceso hijo?** 
  - El proceso hijo pasa al estado de zombie cuando termina.
  - Aunque ya terminó su ejecución, su entrada en la tabla de procesos sigue ocupada, esperando que su padre lea su estado de salida.
- **f.  ¿Quién tendrá la responsabilidad de hacer el wait() si el proceso padre termina sin hacer wait()?** 
  - Si el padre termina antes de hacer `wait()`, el proceso `init` (PID 1) adopta al proceso hijo.
  - Entonces `init` se encarga de hacer el `wait()` correspondiente y liberar los recursos del proceso hijo.
### 9.  Kernel Level Threads 
- **a.  ¿Qué  elementos  del  espacio  de  direcciones  comparten  los  threads  creados  con pthread_create()?** 
  - **Segmento de código (text segment):** todos ejecutan el mismo binario.
  - **Segmento de datos (data segment):** variables globales y estáticas.
  - **Segmento de heap:** memoria dinámica (malloc/new).
  - **Archivos abiertos** (file descriptors).
  - **Dirección del stack del proceso principal** (aunque cada hilo tiene su propio stack).
- **b.  ¿Qué relaciones hay entre getpid() y gettid() en los KLT?** 
  - `getpid()` devuelve el PID del proceso.
  - `gettid()` devuelve el TID del hilo actual, que es único para cada hilo.
  - Todos los hilos de un proceso comparten el mismo `PID`, pero tienen diferentes `TID`.
- **c.  ¿Por  qué  pthread_join()  es  importante  en  programas  que  usan  múltiples  hilos? ¿Cuándo se liberan los recursos de un hilo zombie?** 
  - `pthread_join()` bloquea el hilo que la llama hasta que el hilo objetivo termina.
  - Si no se llama a `pthread_join()`, el hilo puede quedar en estado zombie, reteniendo recursos del sistema
  - También permite:
    - Recuperar el valor de retorno del hilo.
    - Liberar los recursos (memoria, estructuras internas) del hilo terminado.
  - **<u>¿Cuándo se liberan los recursos?</u>**
    - Al hacer pthread_join(): se limpia y recupera el resultado del hilo.
    - O si se hizo previamente un pthread_detach(): el hilo se "desvincula" y sus recursos se liberan automáticamente al finalizar.
- **d.  ¿Qué pasaría si un hilo del proceso bloquea en read()? ¿Afecta a los demás hilos?** 
  - **En Linux moderno (modelo 1:1), donde cada hilo del proceso es un Kernel-Level Thread (KLT):**
    - NO afecta a los demás hilos.
    - Si un hilo bloquea en read(), el kernel bloquea solo ese hilo, no todo el proceso.
    - Los otros hilos siguen ejecutándose normalmente, incluso en paralelo si hay múltiples núcleos.
  - **En sistemas donde se implementan User-Level Threads (ULT) y no hay soporte del kernel para hilos, una syscall bloqueante afecta a todos los hilos del proceso.**
- **e.  Describí qué ocurre a nivel de sistema operativo cuando se invoca pthread_create() (¿es syscall? ¿usa clone?).**
  - **La llamada NO es directamente una syscall.**
  - Usa la syscall `clone()`
  - Se configura `clone()` con flags como:
    - `CLONE_VM` (comparten espacio de memoria)
    - `CLONE_FS`, `CLONE_FILES`, `CLONE_SIGHAND`, etc.
  - El sistema operativo crea una nueva entrada en la tabla de tareas del kernel (como un proceso ligero), con su propio stack y contexto de CPU, pero compartiendo el resto del estado.

**<u>Resultado:</u>** se crea un nuevo hilo del proceso, completamente reconocido por el kernel, planificable en paralelo en otro núcleo.
### 10. User Level Threads  
- **a.  ¿Por qué los ULTs no se pueden ejecutar en paralelo sobre múltiples núcleos?** 
  - El sistema operativo no conoce la existencia de los ULTs: para el kernel, sólo hay un proceso, con un único hilo (KLT).
  - La planificación de los ULTs se hace enteramente en espacio de usuario, por una biblioteca (como GNU Pth)
  - Si el sistema tenga múltiples núcleos, **todos los ULTs del proceso se ejecutan secuencialmente**, el kernel solo asigna 1 CPU al proceso.
- **b.  ¿Qué ventajas tiene el uso de ULTs respecto de los KLTs?** 
  - **Mayor portabilidad:** no dependen del soporte del sistema operativo.
  - **Cambio de contexto más rápido:** se hace en espacio de usuario, sin intervención del kernel.
  - **Ligereza:** no requieren syscalls costosas como clone().
  - **Mayor control sobre la planificación:** el programador puede definir cómo y cuándo se planifican los hilos.
- **c.  ¿Qué relaciones hay entre getpid(), gettid() y pth_self() (en GNU Pth)?** 
  - `getpid()` retorna el PID del proceso (igual para todos los ULTs).
  - `gettid()` no tiene sentido en ULTs puros, ya que el kernel no sabe que hay hilos.
  - `pth_self()` retorna una identificación interna del hilo gestionado por GNU Pth, usada en espacio de usuario.
- **d.  ¿Qué pasaría si un ULT realiza una syscall bloqueante como read()?** 
  - Bloquea a todo el proceso, es decir, todos los ULTs se detienen.
  - Esto ocurre porque el kernel suspende el único hilo visible del proceso, que estaba ejecutando ese ULT.
- **e.  ¿Qué tipos de scheduling pueden tener los ULTs? ¿Cuál es el más común?** 
  - **Cooperativo (cooperative scheduling) – Más común:**
    - El hilo cede voluntariamente el control
    - Más simple de implementar, pero puede tener problemas si un hilo no coopera.
  - **Round-Robin o por prioridades (planificadores personalizados):**
    - La biblioteca puede implementar prioridades, colas de ejecución, etc.
### 11.  Global Interpreter Lock 
- **a.  ¿Qué  es  el  GIL  (Global  Interpreter  Lock)?  ¿Qué  impacto  tiene  sobre  programas multi-thread en Python y Ruby?**
  - El `GIL` **(Global Interpreter Lock)** es un bloqueo a nivel de intérprete que impide que más de un hilo de usuario ejecute código de Python o Ruby al mismo tiempo, incluso en sistemas con múltiples núcleos.
  - **CPython** (la implementación más usada de Python).
  - **MRI** (Matz’s Ruby Interpreter) en Ruby. 
  - **Impacto en programas multithread:**
    - En programas que hacen tareas ligeras de I/O, como acceder a archivos o redes, el GIL no afecta tanto, ya que el hilo se bloquea esperando I/O y otro puede tomar el control.
    - Pero en programas con tareas intensivas de CPU, **el GIL se convierte en un cuello de botella**, ya que:
      - Aunque tengas varios hilos, solo uno ejecuta bytecode a la vez.
      - No hay paralelismo real en múltiples núcleos.
- **b.  ¿Por qué en CPython o MRI se recomienda usar procesos en vez de hilos para tareas intensivas de CPU?**
  - **Porque los procesos NO comparten el GIL**.
  - Cada proceso tiene su propia instancia del intérprete y por tanto su propio GIL.
  - Esto permite que cada proceso pueda correr en un núcleo distinto y ejecutar en paralelo de verdad.
  - **Python:**
    - El módulo `multiprocessing` crea procesos en vez de hilos, permitiendo ejecución concurrente real en tareas pesadas.
  - **Ruby:**
    - Para tareas CPU-intensivas también se suele usar `fork()` o herramientas que crean procesos independientes.
