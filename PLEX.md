En primer lugar, es necesario crear un nuevo directorio /media/pelis y darle los permisos pertinentes , este si miramos atrás nos aparece en el fichero config.yml de flexget, que es el que unirá nuestras descargas con nuestro plex:

![](/IMG/18.PNG)

Con esto, y los cambios realizados en los directorios de flexget y transmission, ya podríamos levantar los contenedores de docker con [docker-compose up -d](docker-compose-up-d.md)

Vamos nuestro navegador y ponemos lo siguiente: localhost:32400/web/index.html

![](/IMG/19.PNG)

---

Se nos indicará para registrarnos con un correo y una contraseña

![](/IMG/20.PNG)

---

Nos saldrá una pestaña emergente para pagar pero no hace falta, continuamos hacia adelante, hasta la pestaña que nos pide señalar que tipo de archivos multimedias vamos a guardar y la ruta donde plex buscará en nuestro servidor para coger dichos archivos, en nuestro caso /media/pelis (como mencioné arriba de este .md).

![](/IMG/21.PNG)

---

Y con esto ya entraríamos a nuestro plex. En algunos casos parece que al volver a entrar nos pone seguir los mismos pasos anteriores. Solo ponemos los mismos datos y quitamos el anterior una vez dentro.

![](/IMG/22.PNG)

Podemos hacer una configuración básica a nuestro Plex desde dentro, en nuestro caso pusimos en las configuraciones generales, biblioteca, que plex sincronizara automáticamente nuestra ruta para ver si hubiera peliculas nuevas.
