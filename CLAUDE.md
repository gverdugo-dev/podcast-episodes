# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repo

Almacén y **feeds públicos** de los episodios de los programas "3 minutos" ("3 minutos de
noticias" hoy; "3 minutos de tecnología" y "3 minutos de deporte" después): el audio y el guion
de cada día de cada programa, más el `feed.xml` de cada uno, que leen Spotify y el resto de
directorios de podcasts. Es un repo de **datos**, no de código: aquí no hay nada que compilar ni
que probar.

- Repo **público** bajo `gverdugo-dev`, servido por **GitHub Pages** desde la raíz de `main`
  (`https://gverdugo-dev.github.io/podcast-episodes/`, o el dominio propio que se configure).
  Submódulo del contenedor `personal-public-resources`.
- **Una carpeta por programa**, con el slug del programa en el agregador (`noticias/`,
  `tecnologia/`...). Dentro viven su `show.json`, su portada, su feed, su web y sus episodios.
  La raíz solo tiene la portada de los programas (`index.html` y `programmes.json`).
- Quien escribe es una **Claude routine**: cada noche, tras escribir el guion de un programa,
  la skill `generate-audio` del plugin `news-aggregator` (repo `plugins-lab`) genera el audio
  con ElevenLabs (con la voz que dice el `show.json` del programa) y la imagen del episodio con
  Nano Banana (a partir del `cover.jpg` del programa, con la maqueta fija del estilo
  `3mn-episode-cover` del plugin `multimedia`), deja los ficheros del día en
  `<slug>/episodes/`, añade el episodio a `<slug>/feed.xml` con `scripts/feed.py` y hace **un
  commit y un push por episodio**.
- El agregador (`news-aggregator`) guarda en su tabla `episodes` a qué programa pertenece cada
  episodio y la URL pública del audio y su duración (`audio_url`, `audio_seconds`), apuntando
  aquí. Es la fuente de verdad de todo lo demás (programa, guion, resumen, keywords, items): en
  este repo solo está lo que se publica.

## Estructura

```
podcast-episodes/
├── CLAUDE.md
├── README.md
├── .nojekyll          # Pages sirve los ficheros tal cual, sin pasar por Jekyll
├── index.html         # la portada: lista los programas (lee programmes.json) y enlaza a cada uno
├── programmes.json    # los programas publicados: slug, nombre, descripción, portada y feed; se edita a mano
└── noticias/          # un programa; su slug en el agregador
    ├── index.html         # la web del programa: hero, lista de episodios (lee su feed.xml en el navegador)
    ├── show.json          # metadatos del programa: nombre, descripción, autor, email, portada, URL base, voz
    ├── cover.jpg          # portada del programa; también la referencia de estilo de las imágenes de episodio
    ├── feed.xml           # el feed RSS del programa, lo mantiene feed.py; un item por episodio
    └── episodes/
        ├── 2026-09-07.mp3     # audio, MP3 44,1 kHz 128 kbps
        ├── 2026-09-07.txt     # guion locutado (enlazado desde el feed como transcripción)
        └── 2026-09-07.jpg     # imagen del episodio (itunes:image del item); puede faltar
```

La fecha del nombre es la del episodio en hora de **Europe/Madrid**, la misma que usa la skill
`write-episode` para decidir si ya hay episodio de hoy de ese programa, y es el `guid` del item
en el feed.

**Por qué los programas van en carpetas y no en la raíz.** El feed de "3 minutos de noticias"
vivió unos días en la raíz (`/feed.xml`) y se movió a `noticias/feed.xml` el 7 de septiembre
de 2026, antes de darlo de alta en Spotify o en ningún directorio: cambiar la URL de un feed
es gratis mientras nadie está suscrito y deja de serlo el día que alguien lo está. El
`base_url` de cada `show.json` lleva la carpeta del programa, y por eso todas las URLs del feed
(audios, imágenes, guiones) la llevan también.

**Dar de alta un programa nuevo** es crear su carpeta con `show.json` (copiar el de
`noticias/` y cambiar nombre, descripción, `base_url`, portada y `voice`), `cover.jpg`,
`index.html` (copia del de `noticias/`) y `episodes/`, y añadirlo a `programmes.json`. El
primer `generate-audio` crea el `feed.xml`.

## Reglas

- **No editar a mano** los ficheros de `<slug>/episodes/` ni `<slug>/feed.xml`: son la salida
  de la routine. Un episodio malo se regenera desde el agregador (`write-episode
  programme=<slug> force=1` y después `generate-audio`), no se retoca aquí. Para corregir el
  audio de un día hay que cambiar el nombre del fichero: Spotify no vuelve a descargar un MP3
  cuya URL no cambia.
- **`<slug>/show.json` y `programmes.json` sí se editan a mano** (son las únicas entradas
  manuales). Tras cambiar un `show.json`, regenerar su feed con `feed.py rebuild --repo .
  --programme <slug>` desde el plugin y commitear los dos. El `email` es el que recibe el
  código de verificación de Spotify y queda público en el XML; `base_url` es la URL desde la
  que se sirven feed y audios (con la carpeta del programa incluida), y cambiarla reescribe
  todas las URLs del feed; `voice` es el nombre de la voz de ElevenLabs con la que se locuta el
  programa (y `voice_id`, opcional, la fija sin buscarla por nombre).
- **Nada del agregador que no se publique**: ni ids, ni keywords, ni items usados, ni claves.
  Este repo es público y su historia también. Las claves (`ELEVENLABS_API_KEY`,
  `NEWS_AGGREGATOR_API_KEY`) viven en el entorno de la routine.
- Mensajes de commit en inglés, con la forma `Episode <slug> YYYY-MM-DD: <título>`.
- Tamaño: unos 3 MB por día. Si el repo crece demasiado, la salida es mover los audios a un
  almacenamiento de objetos y cambiar `base_url`, no borrar historia.

## Publicación

Spotify no tiene API de subida: se le da la URL del feed **una sola vez** (Spotify for Creators,
verificación por código al `email` del feed) y después sondea el feed varias veces por hora. El
diseño completo, incluidas las decisiones sobre contenido generado por IA y cómo se añadirán
otras plataformas, está en `docs/features/publishing.md` del agregador.

## Agent skills

### Issue tracker

Las issues y specs de este recurso **no viven en este repo**: viven en las GitHub Issues del
contenedor privado `gverdugo-dev/personal-public-resources`, con la label `resource:podcast-episodes`,
y se gestionan con la CLI `gh` (cuenta `gverdugo-dev`) pasando siempre
`-R gverdugo-dev/personal-public-resources`. Las PRs sí son de este repo. Ver
`docs/agents/issue-tracker.md`.

### Triage labels

Se usan las cinco etiquetas de triaje por defecto, cada una con su nombre canónico: `needs-triage`,
`needs-info`, `ready-for-agent`, `ready-for-human` y `wontfix`. Ver `docs/agents/triage-labels.md`.

### Domain docs

Single-context: un `CONTEXT.md` y un `docs/adr/` en la raíz del repo, creados solo cuando haga
falta. Ver `docs/agents/domain.md`.
