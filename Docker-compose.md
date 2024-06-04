# Docker-compose

Es una herramienta que se utiliza para definir y ejecutar aplicaciones Docker de múltiples contenedores. Con docker-compose, puedes utilizar un archivo YAML para configurar los servicios de tu aplicación, y luego, con un solo comando, puedes crear y poner en marcha todos estos servicios.

En este caso estableceremos 4 contenedores, PLEX, SAMBA, TRANSMISSION y FLEXGET.

Explicaré cada punto de cada contenedor, por lo que tendré que separarlos unos de otros. Pero pondré al principio como quedaría el archivo.

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

```
- image: dperson/samba: Utiliza la imagen dperson/samba de Docker Hub para configurar un servidor Samba. Esta imagen no tiene etiquetas de versiones específicas, solo la versión latest.

- restart: always: Configura el contenedor para que siempre se reinicie automáticamente si se detiene o falla.

- command: Especifica el comando a ejecutar cuando el contenedor se inicie. Aquí, configura un usuario (juan) con la contraseña (juan_plexÑ) y dos comparticiones de Samba:

    . media mapeado a /media con permisos de lectura/escritura (yes) pero        sin permisos de solo lectura (no).

    . downloads mapeado a /downloads con permisos de lectura/escritura           (yes) pero sin permisos de solo lectura (no).

- stdin_open: true: Mantiene el estándar de entrada (stdin) abierto, lo que permite la interacción con el contenedor si es necesario.

- tty: true: Asigna una terminal pseudo-TTY al contenedor, lo que es útil para la interacción.

- environment: Configura variables de entorno dentro del contenedor. Aquí, establece la zona horaria a Europe/Madrid

- ports: Publica los puertos del contenedor en el host. Samba utiliza los puertos 139 y 445 para compartir archivos. Este mapeo permite acceder a Samba desde el host a través de estos puertos.

- volumes: Mapea directorios del host a directorios dentro del contenedor.

       . ${MEDIA} en el host se mapea a /media en el contenedor.
       . ${STORAGE}/torrents en el host se mapea a /downloads en el                 contenedor.

```
