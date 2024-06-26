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

- **command:** Especifica el comando a ejecutar cuando el contenedor se inicie. Aquí, se configura un usuario con la contraseña y dos comparticiones de Samba:
    - media mapeado a /media con permisos de lectura/escritura (yes) pero sin permisos de solo lectura (no).
    - downloads mapeado a /downloads con permisos de lectura/escritura (yes) pero sin permisos de solo lectura (no).

- **stdin_open:**
  - true: Mantiene el estándar de entrada (stdin) abierto, lo que permite la interacción con el contenedor si es necesario.

- **tty:**
  - true: Asigna una terminal pseudo-TTY al contenedor, lo que es útil para la interacción.

- **environment:** Configura variables de entorno dentro del contenedor. Como la zona horaria

- **ports:** Publica los puertos del contenedor en el host. Samba utiliza los puertos 139 y 445 para compartir archivos. Este mapeo permite acceder a Samba desde el host a través de estos puertos.

- **volumes:** Mapea directorios del host a directorios dentro del contenedor.
  - ${MEDIA} en el host se mapea a /media en el contenedor.
  - ${STORAGE}/torrents en el host se mapea a /downloads en el contenedor.

---

## Plex

Es una aplicación con la que puedes convertir tu ordenador en un centro multimedia utilizando el contenido digital que tengas en él. Reconoce todos los archivos multimedia que tengas alojados en la carpeta de origen, y los organiza con diferentes secciones para que los tengas relativamente organizados independientemente de que los tengas ordenados o no dentro de la carpeta de tu ordenador.

- **image:** jaymoulin/plex:1.30.1.6562
  - Utiliza la imagen jaymoulin/plex de Docker Hub para configurar un servidor Plex.
  - Se especifica la versión 1.30.1.6562 de la imagen.

- **expose:**
  - 32400: Expone el puerto 32400 del contenedor al host.
  - 33400: Expone el puerto 33400 del contenedor al host.

- **ports:**
  - 32400:32400 Mapea el puerto 32400 del host al puerto 32400 del contenedor.
  - 33400:33400 Mapea el puerto 33400 del host al puerto 33400 del contenedor.

- **volumes:**
  - ${STORAGE}/Plex Media Server:/root/Library/Application Support/Plex Media Server -- Mapea el directorio ${STORAGE}/Plex Media Server del host al directorio /root/Library/Application Support/Plex Media Server dentro del contenedor.
  - ${MEDIA}:/media -- Mapea el directorio ${MEDIA} del host al directorio /media dentro del contenedor.
  - ${STORAGE}/tmp:/tmp Mapea el directorio ${STORAGE}/tmp del host al directorio /tmp dentro del contenedor.

- **network_mode:**
  - "host": Utiliza la red del host para el contenedor. Esto solo funciona en sistemas operativos Linux.

---

## Transmission

Transmission sirve para descargar archivos con el protocolo BitTorrent. Es una aplicación de código abierto que se centra en la facilidad de uso en vez de en las funciones adicionales, y es uno de los que menos memoria RAM consume.

- **command:**
  - transmission-daemon -f -g /config:
        Especifica el comando a ejecutar cuando el contenedor se inicie.
        Inicia el demonio de Transmission (transmission-daemon) con las siguientes opciones:
            -f: Ejecuta Transmission en primer plano (foreground).
            -g /config: Especifica la ubicación del archivo de configuración de Transmission en el directorio /config.

- **volumes:** Mapea directorios del host al contenedor.
  - ./transmission:/config: Mapea el directorio ./transmission del host al directorio /config dentro del contenedor, donde       se almacenan los archivos de configuración de Transmission.
  - ${STORAGE}/torrents:/downloads: Mapea el directorio ${STORAGE}/torrents del host al directorio /downloads dentro del         contenedor, donde se guardarán los archivos descargados por Transmission.

- **ports:**
  - Publica los puertos del contenedor en el host.
  - 9091:9091: Mapea el puerto 9091 del host al puerto 9091 del contenedor, que es el puerto de interfaz web de                 Transmission.
  - 51413:51413: Mapea el puerto 51413 del host al puerto 51413 del contenedor, que es el puerto de TCP utilizado por           Transmission para la transferencia de datos.
  - 51413:51413/udp: Mapea el puerto 51413/udp del host al puerto 51413/udp del contenedor, que es el puerto UDP utilizado      por Transmission para la transferencia de datos.

- **expose:** Expone el puerto 9091 del contenedor al mundo exterior. Sin embargo, esto no es necesario ya que el puerto ya está mapeado en la sección de ports.

--- 

## Flexget

FlexGet es una herramienta de gestión de descargas automatizada que te permite automatizar la descarga y el procesamiento de contenido multimedia, como torrents, archivos NZB, feeds de sitios web. Es altamente configurable y se utiliza comúnmente para automatizar la descarga de programas de televisión, películas, música, libros electrónicos y otros tipos de archivos multimedia.

- **image:** 
  - wiserain/flexget:3.5: Utiliza la imagen wiserain/flexget de Docker Hub en su versión 3.5 para configurar el servicio        FlexGet.

- **volumes:** Mapea directorios del host al contenedor.
  - ./flexget:/config: Mapea el directorio ./flexget del host al directorio /config dentro del contenedor, donde se             almacenará la configuración de FlexGet.
  - ./flexget/custom-cont-init.d:/custom-cont-init.d: Mapea el directorio ./flexget/custom-cont-init.d del host al               directorio /custom-cont-init.d dentro del contenedor, donde contiene el script mediainfo.sh
  - ${STORAGE}/torrents:/downloads: Mapea el directorio ${STORAGE}/torrents del host al directorio /downloads dentro del         contenedor, donde se guardarán los archivos descargados.
  - ${MEDIA}:/storage: Mapea el directorio ${MEDIA} del host al directorio /storage dentro del contenedor, donde se             almancenan los archivos descargados
  - 
- **ports:**
    Publica el puerto 5050 del contenedor en el puerto 5050 del host. Puerto para acceder a la interfaz web de FlexGet.

- **environment:** Configura variables de entorno dentro del contenedor.
  - PUID=1001 y PGID=1001: Establece los IDs de usuario y grupo dentro del contenedor.
  - TORRENT_PLUGIN=transmission: Especifica el plugin de Torrent que FlexGet utilizará para las descargas, en este caso, el     plugin de Transmission.
  - FG_WEBUI_PASSWD=123juan_plexÑ: Configura la contraseña para acceder a la interfaz web de FlexGet. Puedes cambiarla.
- **links:**
  - Establece un enlace con transmission. FlexGet necesita acceder al servicio de Transmission para funcionar correctamente.
