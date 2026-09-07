# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repo

Almacén y **feed público** de los episodios de "3 minutos de noticias": el audio y el guion de
cada día, más el `feed.xml` que leen Spotify y el resto de directorios de podcasts. Es un repo
de **datos**, no de código: aquí no hay nada que compilar ni que probar.

- Repo **público** bajo `gverdugo-dev`, servido por **GitHub Pages** desde la raíz de `main`
  (`https://gverdugo-dev.github.io/podcast-episodes/`, o el dominio propio que se configure).
  Submódulo del contenedor `personal-public-resources`.
- Quien escribe es una **Claude routine**: cada noche, tras escribir el guion, la skill
  `generate-audio` del plugin `news-aggregator` (repo `plugins-lab`) genera el audio con
  ElevenLabs y la imagen del episodio con Nano Banana (a partir de `cover.jpg`, con la maqueta
  fija del estilo `3mn-episode-cover` del plugin `multimedia`), deja los ficheros del día en
  `episodes/`, añade el episodio a `feed.xml` con `scripts/feed.py` y hace **un commit y un
  push por episodio**.
- El agregador (`news-aggregator`) guarda en su tabla `episodes` la URL pública del audio y su
  duración (`audio_url`, `audio_seconds`), apuntando aquí. Es la fuente de verdad de todo lo
  demás (guion, resumen, keywords, items): en este repo solo está lo que se publica.

## Estructura

```
podcast-episodes/
├── CLAUDE.md
├── README.md
├── .nojekyll          # Pages sirve los ficheros tal cual, sin pasar por Jekyll
├── index.html         # la web: hero, lista de episodios (lee feed.xml en el navegador) y enlace a gonzaloverdugo.com
├── show.json          # metadatos del programa: nombre, descripción, autor, email, portada, URL base
├── cover.jpg          # portada del programa; también la referencia de estilo de las imágenes de episodio
├── feed.xml           # el feed RSS, lo mantiene feed.py; un item por episodio
└── episodes/
    ├── 2026-09-07.mp3     # audio, MP3 44,1 kHz 128 kbps
    ├── 2026-09-07.txt     # guion locutado (enlazado desde el feed como transcripción)
    └── 2026-09-07.jpg     # imagen del episodio (itunes:image del item); puede faltar
```

La fecha del nombre es la del episodio en hora de **Europe/Madrid**, la misma que usa la skill
`write-episode` para decidir si ya hay episodio de hoy, y es el `guid` del item en el feed.

## Reglas

- **No editar a mano** los ficheros de `episodes/` ni `feed.xml`: son la salida de la routine.
  Un episodio malo se regenera desde el agregador (`write-episode` con `force=1` y después
  `generate-audio`), no se retoca aquí. Para corregir el audio de un día hay que cambiar el
  nombre del fichero: Spotify no vuelve a descargar un MP3 cuya URL no cambia.
- **`show.json` sí se edita a mano** (es la única entrada manual). Tras cambiarlo, regenerar
  el feed con `feed.py rebuild --repo .` desde el plugin y commitear los dos. El `email` es el
  que recibe el código de verificación de Spotify y queda público en el XML; `base_url` es la
  URL desde la que se sirven feed y audios, y cambiarla reescribe todas las URLs del feed.
- **Nada del agregador que no se publique**: ni ids, ni keywords, ni items usados, ni claves.
  Este repo es público y su historia también. Las claves (`ELEVENLABS_API_KEY`,
  `NEWS_AGGREGATOR_API_KEY`) viven en el entorno de la routine.
- Mensajes de commit en inglés, con la forma `Episode YYYY-MM-DD: <título>`.
- Tamaño: unos 3 MB por día. Si el repo crece demasiado, la salida es mover los audios a un
  almacenamiento de objetos y cambiar `base_url`, no borrar historia.

## Publicación

Spotify no tiene API de subida: se le da la URL del feed **una sola vez** (Spotify for Creators,
verificación por código al `email` del feed) y después sondea el feed varias veces por hora. El
diseño completo, incluidas las decisiones sobre contenido generado por IA y cómo se añadirán
otras plataformas, está en `docs/features/publishing.md` del agregador.

## Agent skills

### Issue tracker

Las issues de este repo viven en GitHub Issues de `gverdugo-dev/podcast-episodes` y se gestionan
con el CLI `gh` (cuenta `gverdugo-dev`). Ver `docs/agents/issue-tracker.md`.

### Triage labels

Se usan las cinco etiquetas de triaje por defecto, cada una con su nombre canónico: `needs-triage`,
`needs-info`, `ready-for-agent`, `ready-for-human` y `wontfix`. Ver `docs/agents/triage-labels.md`.

### Domain docs

Single-context: un `CONTEXT.md` y un `docs/adr/` en la raíz del repo, creados solo cuando haga
falta. Ver `docs/agents/domain.md`.
