# podcast-episodes

Audios y feed RSS de **3 minutos de noticias**, el micropodcast diario en castellano que escribe
y locuta cada noche, con inteligencia artificial, la routine del
[agregador de noticias](https://github.com/gverdugo-dev/news-aggregator).

- Feed: `https://gverdugo-dev.github.io/podcast-episodes/feed.xml`
- Cada episodio ocupa dos ficheros en `episodes/`, nombrados por su fecha en hora de Madrid:

| Fichero | Qué es |
|---------|--------|
| `YYYY-MM-DD.mp3` | El audio, MP3 44,1 kHz a 128 kbps, generado con ElevenLabs |
| `YYYY-MM-DD.txt` | El guion tal como se locutó |

Los metadatos del programa están en `show.json`; los de cada episodio (título, resumen, fecha,
duración) viven en `feed.xml`. Aquí no hay código: quien escribe es la skill `generate-audio`
del plugin `news-aggregator` de [`plugins-lab`](https://github.com/gverdugo-dev/plugins-lab),
ejecutada por la routine después de escribir el guion. Un commit por episodio.

El guion lo escribe un modelo de lenguaje a partir de la prensa española del día y la voz es
sintética. El programa lo dice en cada episodio y el feed lo declara con
`<podcast:txt purpose="ai-content">`.
