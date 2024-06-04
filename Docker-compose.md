# Docker-compose

Es una herramienta que se utiliza para definir y ejecutar aplicaciones Docker de múltiples contenedores. Con docker-compose, puedes utilizar un archivo YAML para configurar los servicios de tu aplicación, y luego, con un solo comando, puedes crear y poner en marcha todos estos servicios.

En este caso estableceremos 4 contenedores, PLEX, SAMBA, TRANSMISSION y FLEXGET. **¡¡ TENDREMOS QUE CREAR DICHO docker-compose.yml !!**

Explicaré cada punto de cada contenedor, por lo que tendré que separarlos unos de otros. Pero pondré al principio como quedaría el archivo.

También es importante precisar antes de crear el docker-compose, que utilizaremos y que crearemos un archivo **.env** donde estableceremos las rutas de volumenes que utilizaremos para unir los contenedores, quedando asi:

```
#ruta donde van a ir los archivos renombrados por filebot
MEDIA=/mnt/storage/media
#ruta donde se descargan los archivos del torrent antes de renombrarse
STORAGE=/mnt/storage
```

Y AQUI EL docker-compose.yml:

```
version: "3.7"



services:



  samba:

    image: dperson/samba # no hay tag con versiones, solo latest

    restart: always

    command: '-u "juan;juan_plexÑ" -s "media;/media;yes;no" -s "downloads;/downloads;yes;no"'

    stdin_open: true

    tty: true

    environment:

      - TZ=Europe/Madrid

    ports:

      - 139:139

      - 445:445

    volumes:

      - ${MEDIA}:/media

      - ${STORAGE}/torrents:/downloads



  plex:

    image: jaymoulin/plex:1.30.1.6562

    expose:

      - 32400

      - 33400

    # Descomentar si NO usas network_mode: "host"

    ports:

      - 32400:32400

      - 33400:33400

    environment:

      - TZ=Europe/Madrid

    volumes:

      - ${STORAGE}/Plex Media Server:/root/Library/Application Support/Plex Media Server

      - ${MEDIA}:/media

      - ${STORAGE}/tmp:/tmp

    restart: unless-stopped

    network_mode: "host" # Solo funciona en Linux



  transmission:

    image : jaymoulin/transmission:3.00-1.6.1

    command: transmission-daemon -f -g /config

    volumes:

      - ./transmission:/config

      - ${STORAGE}/torrents:/downloads

    ports:

      - 9091:9091

      - 51413:51413

      - 51413:51413/udp

    expose:

      - 9091

    restart: unless-stopped



  flexget:

    image: wiserain/flexget:3.5

    volumes:

      - ./flexget:/config

      - ./flexget/custom-cont-init.d:/custom-cont-init.d

      - ${STORAGE}/torrents:/downloads

      - ${MEDIA}:/storage

    ports:

      - 5050:5050

    environment:

      - PUID=1001

      - PGID=1001

      - TORRENT_PLUGIN=transmission

      - FG_WEBUI_PASSWD=123juan_plexÑ # la puedes cambiar por la que quieras

    restart: unless-stopped

    links:

      - transmission

```

---

## Samba

Samba es un conjunto de programas que trabajan juntos para permitir a los clientes acceder a espacios de archivos del servidor e impresoras mediante los protocolos SMB (Server Message Block) y CIFS (Common Internet Filesystem).

- **image:** dperson/samba: Utiliza la imagen dperson/samba de Docker Hub para configurar un servidor Samba. Esta imagen no tiene etiquetas de versiones específicas, solo la versión latest.

- **restart:** always: Configura el contenedor para que siempre se reinicie automáticamente si se detiene o falla.

- **command:** Especifica el comando a ejecutar cuando el contenedor se inicie. Aquí, configura un usuario (juan) con la contraseña (juan_plexÑ) y dos comparticiones de Samba:
    - media mapeado a /media con permisos de lectura/escritura (yes) pero sin permisos de solo lectura (no).
    - downloads mapeado a /downloads con permisos de lectura/escritura (yes) pero sin permisos de solo lectura (no).

- **stdin_open:** true: Mantiene el estándar de entrada (stdin) abierto, lo que permite la interacción con el contenedor si es necesario.

- **tty:** true: Asigna una terminal pseudo-TTY al contenedor, lo que es útil para la interacción.

- **environment:** Configura variables de entorno dentro del contenedor. Aquí, establece la zona horaria a Europe/Madrid

- **ports:** Publica los puertos del contenedor en el host. Samba utiliza los puertos 139 y 445 para compartir archivos. Este mapeo permite acceder a Samba desde el host a través de estos puertos.

- **volumes:** Mapea directorios del host a directorios dentro del contenedor.
  - ${MEDIA} en el host se mapea a /media en el contenedor.
  - ${STORAGE}/torrents en el host se mapea a /downloads en el contenedor.

---

## Plex

Es una aplicación con la que puedes convertir tu ordenador en un centro multimedia utilizando el contenido digital que tengas en él. Reconoce todos los archivos multimedia que tengas alojados en la carpeta de origen, y los organiza con diferentes secciones para que los tengas relativamente organizados independientemente de que los tengas ordenados o no dentro de la carpeta de tu ordenador.

- image: jaymoulin/plex:1.30.1.6562
  - Utiliza la imagen jaymoulin/plex de Docker Hub para configurar un servidor Plex.
  - Se especifica la versión 1.30.1.6562 de la imagen.

    expose:
        32400
            Expone el puerto 32400 del contenedor al host.
        33400
            Expone el puerto 33400 del contenedor al host.

    ports:
        32400:32400
            Mapea el puerto 32400 del host al puerto 32400 del contenedor.
        33400:33400
            Mapea el puerto 33400 del host al puerto 33400 del contenedor.

    environment:
        TZ=Europe/Madrid
            Configura la variable de entorno TZ dentro del contenedor con el valor Europe/Madrid.

    volumes:
        ${STORAGE}/Plex Media Server:/root/Library/Application Support/Plex Media Server
            Mapea el directorio ${STORAGE}/Plex Media Server del host al directorio /root/Library/Application Support/Plex Media Server dentro del contenedor.
        ${MEDIA}:/media
            Mapea el directorio ${MEDIA} del host al directorio /media dentro del contenedor.
        ${STORAGE}/tmp:/tmp
            Mapea el directorio ${STORAGE}/tmp del host al directorio /tmp dentro del contenedor.

    restart: unless-stopped
        Configura el contenedor para que se reinicie automáticamente si se detiene o falla, excepto si se detiene manualmente.

    network_mode: "host"
        Utiliza la red del host para el contenedor. Esto solo funciona en sistemas operativos Linux.
