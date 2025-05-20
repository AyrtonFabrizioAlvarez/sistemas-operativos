# Practica 4
## Teoría Docker Compose
### 1.  Utilizando sus palabras describa, ¿qué es docker compose?
**Docker Compose** es una herramienta que permite *definir y administrar múltiples contenedores Docker* como un conjunto único, utilizando un archivo de configuración llamado `docker-compose.yml`.

### 2.  ¿Qué es el archivo compose y cual es su función? ¿Cuál es el “lenguaje” del archivo? 
El archivo Compose (`docker-compose.yml`) es un archivo de texto que *se utiliza para declarar y configurar un conjunto de servicios Docker, junto con sus volúmenes, redes, puertos, y otras opciones de entorno* que permite:  
- **Automatizar** la creación y configuración de múltiples contenedore
- **Centralizar** toda la configuración en un solo archivo
- **Facilitar** la reproducibilidad del entorno
- **Simplificar** el despliegue de entornos complejos con un solo comando `docker compose up`

El archivo `docker-compose.yml` está escrito en `YAML` **(YAML Ain’t Markup Language)**.
- Es un lenguaje de serialización de datos, muy legible para humanos
- Usa identación con espacios (no tabulaciones)
- Utiliza estructuras clave-valor (clave: valor)
- Permite definir listas con -
- No necesita comillas a menos que haya caracteres especiales

**EJEMPLO**  
```yaml
version: '3.9'  # Versión del formato de Compose

services:
  web:
    build: .
    ports:
      - "8080:80"
    volumes:
      - ./html:/usr/share/nginx/html
    image: nginx

  db:
    image: postgres:13
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123

```

### 3.  ¿Cuáles son las versiones existentes del archivo docker-compose.yaml existentes y qué características aporta cada una? ¿Son compatibles entre sí?¿Por qué? 
`docker-compose.yml` tiene versiones especificadas en el campo `version:`, que indican la versión del esquema del archivo, **no la versión de Docker ni de Docker Compose como herramientas.**  
**Principales versiones:**  
| Versión | Características principales                                        | Compatibilidad mínima     |
| ------- | ------------------------------------------------------------------ | ------------------------- |
| `1`     | Muy básica, sin soporte para redes personalizadas ni dependencias. | Docker 1.6 / Compose 1.0  |
| `2`     | Introduce redes personalizadas y dependencias (`depends_on`).      | Docker 1.10 / Compose 1.6 |
| `2.1`   | Mejora el soporte de logging y opciones de reinicio (`restart`).   | Docker 1.12 / Compose 1.8 |
| `2.2`   | Añade control sobre `cpu_count`, `cpu_percent` y otros recursos.   | Docker 1.13.0+            |
| `2.3`   | Agrega mejoras para `deploy` (limitado en Docker Compose clásico). | Docker 17.06+             |
| `3`     | Pensada para producción con Docker Swarm. Introduce `deploy`.      | Docker 1.13.0+            |
| `3.1`   | Añade soporte para `configs` en Swarm.                             | Docker 1.13.1+            |
| `3.2`   | Soporte para `long syntax` de `volumes`, mejora de `build`.        | Docker 17.04+             |
| `3.3`   | Introduce `secret` para mantener datos sensibles.                  | Docker 17.06+             |
| `3.4`   | Añade mejoras para `configs` y compatibilidad con Swarm.           | Docker 17.09+             |
| `3.5`   | Mejoras en `healthcheck` y opciones de red.                        | Docker 17.12+             |
| `3.6`   | Mejora `deploy`, `placement`, `resources`.                         | Docker 18.02+             |
| `3.7`   | Nuevas opciones de volúmenes, networks y comandos de ejecución.    | Docker 18.06+             |
| `3.8`   | Más configuraciones de red y recursos.                             | Docker 19.03+             |
| `3.9`   | Última versión clásica (sin cambios estructurales mayores).        | Docker 20.10+             |

Cada versión tiene su propio conjunto de características. Algunos campos que existen en `version: 3.8` no están disponibles si usás `version: 2.1`, por ejemplo:  
| Campo / Feature         | Versión 2.x | Versión 3.x                              |
| ----------------------- | ----------- | ---------------------------------------- |
| `restart`               | ✅           | ✅                                        |
| `deploy`                | ❌           | ✅ (solo con Swarm)                       |
| `depends_on`            | ✅           | ⚠️ Parcial (sin control de orden en 3.x) |
| Control fino de CPU/RAM | ✅           | ❌ (en 3.x solo con `deploy`)             |
| `volumes`, `networks`   | ✅           | ✅                                        |

- Para **desarrollo**: usá `2.4` con `depends_on` si necesitás controlar orden sin complicarte.
- Para **producción** con `Swarm`: no confíes en `depends_on`, usá `healthcheck` + scripts de espera.

**La mejor versión depende del caso de uso: `2.4` para desarrollo clásico, `3.8` para producción con Swarm.**


### 4.  Investigue y describa la estructura de un archivo compose. Desarrolle al menos sobre los siguientes bloques indicando para qué se usan: 
```yml
version: "3.8"
services:
  servicio1:
    image: ...
    ports: ...
    environment: ...
  servicio2:
    build: ...
    volumes: ...
volumes:
  volumen1:
networks:
  red1:
```
- **a.  services**
  - El bloque `services` define los contenedores que se van a levantar, cada uno de estos puede tener su propia condiguración
  ```yml
    services:
      web:
        image: nginx
        ports:
          - "8080:80"
      db:
            image: postgres
    ```
- **b.  build** 
  - Se usa para construir una imagen desde un `Dockerfile` ubicado en un directorio.
     ```yml
    services:
      app:
        build:
          context: ./app
          dockerfile: Dockerfile.dev
    ```
- **c.  image** 
  - Especifica una imagen existente para usar directamente sin necesidad de build.
   ```yml
    services:
      nginx:
      image: nginx:latest
    ```
- **d.  volumes** 
  - Permite montar volúmenes persistentes o vincular carpetas del host con rutas del contenedor.
   ```yml
    services:
      app:
        volumes:
          - ./html:/var/www/html
          - datos:/datos

    volumes:
    datos:
    ```
    **Tipos:**  
    - host path: carpeta del host
    - named volume: declarado en la parte inferior (volumes:)
- **e.  restart** 
  - Define la política de reinicio del contenedor cuando falla o se detiene.
   ```yml
    services:
      db:
        image: postgres
        restart: always
    ```
    | Opción           | Descripción                                                        |
    | ---------------- | ------------------------------------------------------------------ |
    | `no` (default)   | No reinicia nunca.                                                 |
    | `always`         | Siempre reinicia si se detiene.                                    |
    | `on-failure`     | Solo si falla con código distinto de 0.                            |
    | `unless-stopped` | Igual que `always`, pero no se reinicia si lo detenés manualmente. |

- **f.  depends_on** 
  - Especifica el orden de arranque entre servicios.
   ```yml
  services:
    web:
      depends_on:
        - db
    db:
      image: postgres
    ```
    **Solo garantiza orden de inicio, no espera a que el servicio esté "listo"**. Para eso, necesitás `healthcheck` o scripts (`wait-for-it.sh`).  
- **g.  environment** 
  - Define variables de entorno del contenedor (usadas por la app o la imagen base)
   ```yml
    services:
      web:
        image: nginx
        ports: 
          - "8080:80"
      db:
        image: postgres
        environment:
          - POSTGRES_USER=admin
          - POSTGRES_PASSWORD=1234
    ```
- **h.  ports** 
  - Define el mapeo de puertos del host al contenedor.
   ```yml
    services:
      web:
        image: nginx
        ports:
            - "8080:80"  # el puerto 8080 del host redirige al puerto 80 del contenedor.
            - "3030:3001" # pueden mapearse varios puertos
            - "443:443"
      db:
        image: postgres
    ```
- **i.  expose** 
  - Expone puertos dentro de la red Docker, pero no hacia el host.
  - Sirve para que otros contenedores en la misma red puedan acceder a ese puerto, pero no está disponible desde el navegador ni localhost.
  - Es como decir "este contenedor publica este puerto internamente", a diferencia de ports, que lo expone al sistema operativo host.
   ```yml
    services:
      web:
        image: nginx
        expose:
          - "80"
      db:
        image: postgres
    ```
- **j.  networks** 
  - Define las redes internas entre contenedores, lo que permite su comunicación por nombre. POr defecto se utiliza Bridge
   ```yml
  services:
    web:
      networks:
        - backend
    db:
      networks:
        - backend

  networks:
    backend:
    ```
| Bloque        | Para qué se usa                            |
| ------------- | ------------------------------------------ |
| `services`    | Define los contenedores que usarás.        |
| `build`       | Construye una imagen desde un Dockerfile.  |
| `image`       | Usa una imagen existente.                  |
| `volumes`     | Monta carpetas o volúmenes persistentes.   |
| `restart`     | Controla cuándo se reinicia el contenedor. |
| `depends_on`  | Orden de arranque entre contenedores.      |
| `environment` | Variables de entorno del contenedor.       |
| `ports`       | Expone puertos del contenedor al host.     |
| `expose`      | Expone puertos solo a otros contenedores.  |
| `networks`    | Redes internas entre contenedores.         |


### 5. Conceptualmente: ¿Cómo se podrían usar los bloques “healthcheck” y “depends_on” para ejecutar una aplicación Web dónde el backend debería ejecutarse si y sólo si la base de datos ya está ejecutándose y lista? 
Esto no se logra únicamente con `depends_on`, porque:  
- `depends_on` sólo espera que el contenedor esté creado y arrancado, no que el servicio esté listo (por ejemplo, que la base de datos acepte conexiones).
- Por eso se combina con `healthcheck`, que permite determinar si un servicio está realmente operativo.

1. La base de datos define un bloque healthcheck, donde le indicás cómo saber si está "saludable" (ej., conectarse a sí misma con pg_isready).

2. El backend puede definirse para esperar hasta que la base esté "healthy".

3. Usás un script de espera (wait-for-it.sh, wait-for) en el backend o bien, implementás la lógica dentro del propio backend (p.ej. reintentos de conexión).

**EJEMPLO:**  
```yml
version: "3.8"

services:
  db:
    image: postgres:15
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: 1234
    volumes:
      - db_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "admin"]
      interval: 5s
      timeout: 3s
      retries: 5

  backend:
    build: ./backend
    depends_on:
      db:
        condition: service_healthy
    environment:
      DATABASE_URL: postgres://admin:1234@db:5432/postgres
    ports:
      - "5000:5000"

volumes:
  db_data:
```
### 6.  Indique qué hacen y cuáles son las diferencias entre los siguientes comandos: 
- **a.  docker compose create  y docker compose up**  
    | Comando                 | ¿Qué hace?                                                                                              |
    | ----------------------- | ------------------------------------------------------------------------------------------------------- |
    | `docker compose create` | **Crea** los contenedores definidos en el `docker-compose.yml` pero **no los inicia**.                  |
    | `docker compose up`     | Crea (si no existen) **e inicia los contenedores** y sus servicios. También hace build si es necesario. |

- **b.  docker compose stop  y docker compose down**  
    | Comando | ¿Qué hace?                                                                                     |
    | ------- | ---------------------------------------------------------------------------------------------- |
    | `stop`  | **Detiene** los contenedores, pero **los deja creados** (pueden reiniciarse con `start`).      |
    | `down`  | **Detiene y elimina**: contenedores, redes creadas automáticamente, y opcionalmente volúmenes. |

- **c.  docker compose run  y docker compose exec**  
    | Comando | ¿Qué hace?                                                                                                  |
    | ------- | ----------------------------------------------------------------------------------------------------------- |
    | `run`   | Lanza un **contenedor nuevo temporal** de un servicio (no el que está en ejecución) para correr un comando. |
    | `exec`  | Ejecuta un comando **dentro de un contenedor en ejecución** del servicio.                                   |

- **d.  docker compose ps**  
  - Muestra el estado de los contenedores definidos en el archivo `docker-compose.yml`.
    - Qué servicios están corriendo
    - Puertos expuestos
    - Estado (Up, Exited, etc.)

- **e.  docker compose logs**  
  - Muestra los logs (salida de consola) de todos los contenedores definidos en docker-compose.yml.
    - Ver logs de un servicio en particular.
        ```bash
            docker compose logs              # logs de todo
        ```
    - Usar -f para seguirlos en tiempo real (como tail -f).
        ```bash
            docker compose logs -f backend  # logs en vivo solo del backend
        ```

### 7.  ¿Qué tipo de volúmenes puede utilizar con docker compose? ¿Cómo se declara cada tipo en el archivo compose? 
1. Bind Mounts (Montajes de host)
   - Montás un directorio o archivo del host directamente en un contenedor.
   - Ideal para desarrollo: ves los cambios en vivo desde tu editor.
       ```yml
       services:
       web:
           volumes:
           - ./html:/usr/share/nginx/html  #HOST_PATH:CONTAINER_PATH
       ```
   - En este ejemplo, monta la carpeta local **`./html` del host en `/usr/share/nginx/html` dentro del contenedor**.

2. **Named Volumes (Volúmenes gestionados por Docker)**
   - Docker los administra y guarda en /var/lib/docker/volumes/
   - Son persistentes entre reinicios y más portables para producción.
        ```yml
        services:
            db:
            volumes:
                - datos_postgres:/var/lib/postgresql/data  #VOLUME_NAME:CONTAINER_PATH
        volumes:
            datos_postgres:
        ```
   - En este ejemplo,** `datos_postgres` es el nombre lógico del volumen**.

### 8.  ¿Qué sucede si en lugar de usar el comando “docker compose down” utilizo “docker compose down -v/--volumes”? 
El comando `docker compose down`  
- Detiene y elimina todos los contenedores, redes y otros recursos relacionados al archivo docker-compose.yml en el directorio actual.
- Pero **NO** elimina los volúmenes.


El comando `docker compose down -v`  
- Hace lo mismo que el anterior pero además elimina los volúmenes anónimos y nombrados creados por Compose.
- Esto implica:
  - Borra completamente los datos persistidos en esos volúmenes.
  - Deja el entorno como nuevo, como si nunca hubieras creado esos volúmenes.

| Característica          | Bind Mount                 | Named Volume                  |
| ----------------------- | -------------------------- | ----------------------------- |
| Definición del path     | Vos lo elegís              | Docker lo maneja internamente |
| Persistencia controlada | Por vos (depende del host) | Por Docker                    |
| Borrado con `down -v`   | ❌ No se borra              | ✅ Se borra                    |
| Ideal para...           | Desarrollo                 | Producción y persistencia     |
| Soporta hot reload      | ✅ Sí                       | ❌ No directamente             |


## Práctica Docker Compose

- **¿Cuántos contenedores se instancian?** 
  - Se instancian 2 contenedores, `db` y `wordpress`
- **¿Por qué no se necesitan Dockerfiles?** 
  - No se utiliza Dockerfile porque al parecer es suficiente con las imagenes standar de Docker Registry
- **¿Por qué el servicio identificado como “wordpress” tiene la siguiente línea?**
    ```yml
        depends_on: 
          - db 
    ```
  - Esta línea indica que el servicio `wordpress` depende del servicio `db`, es decir, que Docker Compose debe instanciar primero `db`.
  - Pero: En versiones `3.x`, `depends_on` solo controla el orden de inicio de los contenedores, no espera a que db esté listo (por ejemplo, que MySQL haya terminado de levantar y esté escuchando).

- **¿Qué volúmenes y de qué tipo tendrá asociado cada contenedor?** 
  - `db`
    - db_data:/var/lib/mysql 
      - es del tipo named volume, su nombre lógico para docker compose es `db_data`
  - `wordpress`
      - ${PWD}:/data 
        - es del tipo bind mount, mapea el directorio actual del host al contenedor, útil para acceder a los archivos locales.
      - wordpress_data:/var/www/html 
        - es del tipo named volume, su nombre lógico para docker compose es `wordpress_data`
- **¿Por que uso el volumen nombrado de la siguiente manera para el servicio db en lugar de dejar que se instancie un volumen anónimo con el contenedor?** 
    ```yml
        volumes: 
          - db_data:/var/lib/mysql 
    ```
    - **Un volumen nombrado:**
      - Se define explícitamente en el archivo.
      - Persiste entre ejecuciones.
      - Puede ser reutilizado por otros contenedores.
    - **Un volumen anónimo (si no se define):**
      - Tiene un nombre aleatorio.
      - Es difícil de gestionar o reutilizar.
      - Puede eliminarse sin querer.
- **¿Qué genera la línea siguiente en la definición de wordpress?** 
    ```yml
        volumes: 
          - ${PWD}:/data 
    ```
    - Usa la variable de entorno `${PWD}` para montar el directorio actual del host.
    - Lo monta dentro del contenedor en `/data`. Esto permite, por ejemplo, compartir archivos desde el host al contenedor en tiempo real.

- **¿Qué representa la información que estoy definiendo en el bloque environment de cada servicio?¿Cómo se “mapean” al instanciar los contenedores?**  
  - Este bloque define variables de entorno dentro del contenedor.
    ```yml
    environment:
      - MYSQL_DATABASE: wordpress
    ```
  - Dentro del contenedor, esta variable estará disponible como:
    ```bash
    echo $MYSQL_DATABASE  # salida: wordpress
    ```
  - Sirve para pasarle configuración interna a la app que corre dentro del contenedor.


- **¿Qué sucede si cambio los valores de alguna de las variables definidas en bloque “environment” en solo uno de los contenedores y hago que sean diferentes? (Por ej: cambio SOLO en la definición de wordpress la variable WORDPRESS_DB_NAME)**  
  - Si los valores no coinciden entre `db` y `wordpress`, WordPress no podrá conectarse a la base de datos, porque buscará otra base o se intentará autenticar con datos incorrectos.

- **¿Cómo sabe comunicarse el contenedor “wordpress” con el contenedor “db” si nunca doy información de direccionamiento?**  
  - Porque ambos están en la misma red de Docker `wordpress`. Docker resuelve el nombre del servicio como un hostname interno
  - por ejemplo: `ping db` funciona como DNS interno.

- **¿Qué puertos expone cada contenedor según su Dockerfile? (pista: navegue el sitio https://hub.docker.com/_/wordpress y https://hub.docker.com/_/mysql para acceder a los Dockerfiles que generaron esas imágenes y responder esta pregunta.)**  
  - `wordpress` expone el puerto `80` del contenedor
  - `db` expone el puerto `3306` del contenedor
 
- **¿Qué servicio se “publica” para ser accedido desde el exterior y en qué puerto?¿Es necesario publicar el otro servicio? ¿Por qué?**  
  - Expone el puerto `80` del contenedor `wordpress` en el puerto `8000` del `host`.
  - Se puede acceder en el navegador mediante: http://localhost:8000.
  - No es necesario exponer db hacia el exterior porque es usado internamente, solo por WordPress. No se espera conexión desde el host ni desde otros servicios externos, es por eso que directamente nuestro `docker-compose.yml` no tiene una linea que especifique el `port` (EXPONER NO ES LO MISMO QUE PUBLICAR PA)
 