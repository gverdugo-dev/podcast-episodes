<p align="center">
  <img src="docs/banner.jpg" alt="Una torre de radio art déco en vector plano" width="100%">
</p>

# podcast-episodes

Audios, guiones y feeds RSS de los programas **3 minutos** ("3 minutos de noticias" hoy;
tecnología y deporte después), los micropodcasts diarios en castellano que escribe y locuta
cada noche, con inteligencia artificial, la routine del
[agregador de noticias](https://github.com/gverdugo-dev/news-aggregator).

| | |
|---|---|
| **Qué es** | Repo de datos servido por GitHub Pages: una carpeta por programa con un MP3, un guion y una imagen por día, más su feed |
| **Estado** | Activo; escribe la routine, un commit por episodio |
| **Stack** | Ficheros estáticos, un `feed.xml` RSS por programa con las etiquetas de iTunes y Podcasting 2.0, `index.html` |
| **Repo** | `gverdugo-dev/podcast-episodes`, público |
| **Forma parte de** | `personal-public-resources`, el contenedor de recursos personales |

## Qué hace

- Portada de los programas: `https://gverdugo-dev.github.io/podcast-episodes/`
- Feed de "3 minutos de noticias": `https://gverdugo-dev.github.io/podcast-episodes/noticias/feed.xml`
- Web de "3 minutos de noticias": `https://gverdugo-dev.github.io/podcast-episodes/noticias/`

Cada programa tiene su carpeta, con el slug que tiene en el agregador, y cada episodio ocupa
hasta tres ficheros en `<slug>/episodes/`, nombrados por su fecha en hora de Madrid:

| Fichero | Qué es |
|---------|--------|
| `YYYY-MM-DD.mp3` | El audio, MP3 44,1 kHz a 128 kbps, generado con ElevenLabs |
| `YYYY-MM-DD.txt` | El guion tal como se locutó |
| `YYYY-MM-DD.jpg` | La imagen del episodio, generada con Nano Banana a partir de la portada (puede faltar) |

El guion lo escribe un modelo de lenguaje a partir de la prensa española del día y la voz es
sintética. El programa lo dice en cada episodio y el feed lo declara con
`<podcast:txt purpose="ai-content">`.

## Cómo se usa

Aquí no hay código que ejecutar. Para escuchar un programa basta con dar la URL de su feed a
cualquier aplicación de podcasts. Las únicas entradas manuales son `programmes.json` (la lista
de programas de la portada) y el `show.json` de cada programa (nombre, descripción, autor,
email, portada, URL base, voz); tras cambiar un `show.json` se regenera su feed desde el plugin
y se commitean los dos. Los ficheros de `<slug>/episodes/` y `<slug>/feed.xml` no se editan a
mano: un episodio malo se regenera desde el agregador.

## Estructura

```
docs/                  # esta cabecera
index.html             # la portada: lista de programas leída de programmes.json
programmes.json        # los programas publicados: slug, nombre, descripción, portada y feed
noticias/              # un programa, con su slug del agregador
  episodes/            # un MP3, un guion y una imagen por día
  show.json            # metadatos del programa, incluida la voz
  cover.jpg            # portada del programa y referencia de estilo de las imágenes de episodio
  feed.xml             # el feed RSS del programa, un item por episodio
  index.html           # la web del programa: lista de episodios leída del feed en el navegador
.nojekyll              # Pages sirve los ficheros tal cual
```

## Cómo encaja

Es la salida pública del sistema del micropodcast. Quien escribe es la skill `generate-audio`
del plugin `news-aggregator` de [`plugins-lab`](https://github.com/gverdugo-dev/plugins-lab),
ejecutada por la routine después de escribir el guion: genera el audio, deja los ficheros del
día, añade el episodio al feed y registra en el agregador la URL y la duración. Spotify y los
demás directorios leen este feed.

## Más información

Las reglas del repo y el flujo de publicación están en `CLAUDE.md`. El diseño completo, incluidas
las decisiones sobre contenido generado por IA, está en `docs/features/publishing.md` del
agregador.
