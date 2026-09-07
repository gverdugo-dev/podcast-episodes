# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Qué es este repo

Almacén de los **episodios de "3 minutos de noticias"**: el audio, el guion y los metadatos de
cada día. Es un repo de **datos**, no de código: aquí no hay nada que compilar ni que probar.

- Repo privado bajo `gverdugo-dev`. Submódulo del contenedor `personal-public-resources`.
- Quien escribe es una **Claude routine**: cada noche, tras escribir el guion, la skill
  `generate-audio` del plugin `news-aggregator` (repo `plugins-lab`) genera el audio con
  ElevenLabs, deja los tres ficheros del día en `episodes/` y hace **un commit y un push por
  episodio**.
- El agregador (`news-aggregator`) guarda en su tabla `episodes` la URL del audio y su duración
  (`audio_url`, `audio_seconds`), apuntando a este repo.

## Estructura

```
podcast-episodes/
├── CLAUDE.md
├── README.md
└── episodes/
    ├── 2026-09-07.mp3     # audio, MP3 44,1 kHz 128 kbps
    ├── 2026-09-07.txt     # guion locutado
    └── 2026-09-07.json    # metadatos del episodio
```

La fecha del nombre es la del episodio en hora de **Europe/Madrid**, la misma que usa la skill
`write-episode` para decidir si ya hay episodio de hoy.

## Reglas

- **No editar a mano** los ficheros de `episodes/`: son la salida de la routine. Un episodio
  malo se regenera desde el agregador (`write-episode` con `force=1` y después `generate-audio`),
  no se retoca aquí.
- **Nunca** guardar aquí la clave de ElevenLabs ni la del agregador. Viven en el entorno de la
  routine (`ELEVENLABS_API_KEY`, `NEWS_AGGREGATOR_API_KEY`).
- Mensajes de commit en inglés, con la forma `Episode YYYY-MM-DD: <título>`.
- Tamaño: unos 3 MB por día. Si el repo crece demasiado, la salida es mover los audios a un
  almacenamiento de objetos y dejar aquí solo guion y metadatos, no borrar historia.

## Lo que vendrá

La publicación en Spotify necesita un feed RSS con URLs públicas de los audios. Si este repo se
hace público, GitHub Pages puede servir el feed y los mp3 desde aquí mismo.
