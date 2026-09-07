# podcast-episodes

Audios diarios de **3 minutos de noticias**, el micropodcast que escribe cada noche la routine del
[agregador de noticias](https://github.com/gverdugo-dev/news-aggregator).

Cada episodio ocupa tres ficheros en `episodes/`, nombrados por su fecha en hora de Madrid:

| Fichero | Qué es |
|---------|--------|
| `YYYY-MM-DD.mp3` | El audio, MP3 44,1 kHz a 128 kbps, generado con ElevenLabs |
| `YYYY-MM-DD.txt` | El guion tal como se locutó |
| `YYYY-MM-DD.json` | Metadatos: id del episodio en el agregador, título, resumen, keywords, items usados, segundos |

Aquí no hay código. Quien escribe es la skill `generate-audio` del plugin `news-aggregator` de
[`plugins-lab`](https://github.com/gverdugo-dev/plugins-lab), ejecutada por la routine después de
escribir el guion. Un commit por episodio.
