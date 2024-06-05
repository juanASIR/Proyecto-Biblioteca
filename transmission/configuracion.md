Al igual que en flexget, al levantar bien el docker-compose se crea un directorio llamado transmission. En este directorio solo tendremos que modificar el archivo **settings.json**.

Este archivo configura varios aspectos de cómo Transmission gestiona torrents, desde límites de velocidad, configuración de red, manejo de archivos y seguridad:

  - alt-speed-down y alt-speed-up: Define velocidades alternativas para descarga (100 KB/s) y subida (20 KB/s).

  - alt-speed-enabled: Habilita las velocidades alternativas.

  - alt-speed-time-begin y alt-speed-time-end: Especifica el inicio (660 minutos desde la medianoche, es decir, 11:00 AM) y el fin (300 minutos desde la medianoche, es decir, 5:00 AM) del periodo en que se aplican las velocidades alternativas.

  - alt-speed-time-day: Los días de la semana en los que se aplican las velocidades alternativas (127 significa todos los días).

  - bind-address-ipv4 y bind-address-ipv6: Direcciones IP a las que se vincula Transmission para IPv4 y IPv6.

  - blocklist-enabled: Deshabilita la lista de bloqueo.

  - blocklist-url: URL de la lista de bloqueo.

  - cache-size-mb: Tamaño de la caché en MB (4 MB).

  - dht-enabled: Habilita DHT (Distributed Hash Table) para buscar pares.

  - download-dir: Directorio de descarga completada (/downloads/complete).

  - download-queue-enabled: Habilita la cola de descargas.

  - download-queue-size: Tamaño de la cola de descargas (5 torrents).

  - encryption: Nivel de cifrado (1 significa "preferir cifrado").

  - idle-seeding-limit: Límite de tiempo para sembrado inactivo (300 minutos).

  - incomplete-dir y incomplete-dir-enabled: Directorio de descargas incompletas (/downloads/incomplete) y su habilitación.

  - lpd-enabled: Deshabilita LPD (Local Peer Discovery).

  - message-level: Nivel de detalle de los mensajes (1 es mínimo).

  - peer-limit-global y peer-limit-per-torrent: Límites global y por torrent de pares (200 y 50 respectivamente).

  - peer-port: Puerto utilizado por pares (51413).

  - pex-enabled: Habilita PEX (Peer Exchange).

  - port-forwarding-enabled: Habilita el reenvío de puertos.

  - queue-stalled-enabled: Habilita la cola de torrents estancados.

  - queue-stalled-minutes: Tiempo para considerar un torrent como estancado (30 minutos).

  - ratio-limit y ratio-limit-enabled: Límite de ratio de subida/bajada (1.1) y su habilitación.

  - rpc-authentication-required: Requiere autenticación RPC.

  - rpc-bind-address: Dirección IP a la que se vincula RPC (0.0.0.0).

  - rpc-enabled: Habilita la interfaz RPC.

  - rpc-port: Puerto utilizado para RPC (9091).

  - rpc-username y rpc-password: Nombre de usuario y contraseña para RPC (admin y una contraseña cifrada).

  - scrape-paused-torrents-enabled: Habilita la obtención de datos de torrents pausados.

  - seed-queue-enabled: Habilita la cola de siembra.

  - seed-queue-size: Tamaño de la cola de siembra (5 torrents).

  - speed-limit-down y speed-limit-up: Límites de velocidad de descarga (100 KB/s) y subida (200 KB/s).

  - start-added-torrents: Inicia torrents añadidos automáticamente.

  - trash-original-torrent-files: Elimina archivos .torrent originales.

  - umask: Máscara de permisos de archivos (2).

  - upload-slots-per-torrent: Ranuras de subida por torrent (14).

  - utp-enabled: Habilita µTP (Micro Transport Protocol).

  - watch-dir y watch-dir-enabled: Directorio vigilado (/to_download) y su habilitación.
