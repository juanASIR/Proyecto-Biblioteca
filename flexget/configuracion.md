Una vez hemos levantado el docker-compose y funcionado, y luego bajarlo, veremos que aparecen en la misma ubicación donde lo levantamos el directorio de flexget, en el cual se encuentran varios archivos de los cuales solo tendremos que modificar uno y crear otro. 

![](/IMG/31.PNG)

El primero es el **config.yml** donde configuraremos puntos como:

  - Habilita el servidor web de FlexGet para permitir la gestión de tareas y configuración a través de una interfaz web.
  - Configura todas las tareas para que se ejecuten cada 30 minutos. Esto asegura que las tareas de FlexGet se ejecuten regularmente sin intervención manual.
  - Establece un listado de series, con requisitos.
  - Define los parámetros de conexión para Transmission, el cliente BitTorrent, incluyendo el host, puerto, nombre de usuario y contraseña.
  - Tarea sort_tv: Ordena series descargadas de /downloads/complete a /storage/series. Usa mediainfo (En el siguiente punto mostramos el script) para verificar el codec de audio y, si es EAC3, convierte el audio a AC3 con ffmpeg. Si no, simplemente copia el archivo. Usa guessit para el análisis de series, thetvdb_lookup para buscar información de TVDB y asegura que solo se procesen entradas con series_name.
  - Tarea sort_movies: Similar a sort_tv, pero para películas. Ordena películas de /downloads/complete a /storage/pelis, usando mediainfo y ffmpeg para convertir el audio si es necesario. Usa tmdb_lookup para buscar información en The Movie Database (TMDB) y asegura que solo se procesen entradas con movie_name.
  - Tarea remove_stale_torrents: Gestiona torrents en Transmission, aceptando aquellos con progreso completo, rechazando los que no cumplen con el ratio de seed o el límite de inactividad, y purgando los torrents completados. Desactiva los plugins seen y seen_info_hash para evitar conflictos con entradas ya procesadas.

---

Lo siguiente sería entrar en el directorio de custom-cont-init.d y crear el script **mediainfo.sh**, el cual servirá para :

  - Obtener información técnica y etiquetas sobre archivos multimedia (audio y video).
  - Y con ffmpeg: Para manejar multimedia, usado para convertir, grabar, y transmitir archivos de audio y video.
  - Es decir prepara el entorno del contenedor instalando estas dos herramientas esenciales para la gestión y procesamiento de archivos multimedia.
