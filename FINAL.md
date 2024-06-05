Tenemos la pelicula descargada en nuestro host local, pero la idea es pasarlo a nuestro plex. Es verdad que dependiendo de algunos factores como el tamaño del archivo, su formato, la cola de descarga que haya, etc.. Este puede tardar en sincronizarse con plex, y por tanto con su volumen /media/pelis.

Para ello, vamos a forzarlo con el siguiente comando:

```
docker-compose exec flexget flexget execute --task sort_movies --debug

```

![](/IMG/26.PNG)

---

Si aparece asi es que ha sido capaz de sincronizarlo. Por lo tanto no solo aparecerá en el volumen /media/pelis (Que creamos al principio por eso mismo), sino también en nuestro plex.

![](/IMG/27.PNG)

---

![](/IMG/28.PNG)

---

![](/IMG/29.PNG)

---

![](/IMG/30.PNG)

---

Como vemos, no solo aparece la pelicua en nuestro plex y puede visualizarse (previa instalacion de vlc), sino que tambien aparece una descripción general de dicha película.

