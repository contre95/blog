---
title: Jardín de Música
date: 2025-06-12  00:18:54
tags:
    - Music
    - Tech
    - Self-Hosted
---

![Intro](/images/posts/music_banner.png)

Habrá sido en 2009 cuando le pregunté a mi prima más grande qué música escuchaba. Fue la primera vez que asocié de manera concreta la idea de _banda_ con _canción_. Hasta entonces, mi vínculo con la música venía más por ósmosis que por elección propia. Más tarde iba entrar en razón que lo que escuchaba hasta entonces era mucho rock anglosajón, algo de blues y algo de jazz, todo bastante internacional y descontextualizado. Principalmente escuchaba Aspen, una radio que mezclaba (y sigue mezclando) clásicos del soft rock con algunas baladas en inglés y algo de pop. Mi viejo era quien manejaba el dial, y eso definía buena parte de lo que entraba a casa, mucho Genesis, Phil Collins, Pink Floyd, Eric Clapton entre otras bandas mas de buto como Nora Jones, Bosa Nova y demás, que hoy en día me cuesta más escuchar. Lo argentino llegaba más por Sumo, algo de La Bersuit y Serú Girán. Pero el rock nacional más contemporáneo me lo presentaron mis amigos, bandas como Árbol, Airbag y Ataque 77, sonaban alternando con cosas como la cumbia y el reggaeton del momento como Daddy Yankee, Wisin y Yandel y demás música puertorriqueña.

Fue cuando tuve mi primera computadora que empecé a elegir un poco qué escuchar. Mi carpeta de música fue mi primer gran archivo: era algo curado, donde no había espacio para albumes completos ni canciones que no me gustasen. La primera oleada de música nació como fruto de mi memoria y de [Ares Galaxy](https://es.wikipedia.org/wiki/Ares_Galaxy). Ahí entraron bandas como las que mencioné arriba. Las que más tardaron en llegar fueron las que escuchaba por la radio; de esas no tenía el nombre. Muchas regresaban sin nombre, escondidas en los pendrives que compartíamos con mis amigos. Solo al reproducirlas descubría si me eran familiares. Más adelante, aplicaciones como [Sound Hound](https://play.google.com/store/apps/details?id=com.melodis.midomiMusicIdentifier.freemium&hl=en-US&pli=1) o [Shazam](https://www.shazam.com/) me iban a auxiliar con la categorización.

Desde ese entonces, al día de hoy, esa misma carpeta fue sufriendo varias iteraciones, no solo en variedad y calidad de contenido, sino en su categorización y organización. Nunca se borró por error, nunca se perdió y nunca murío, solo mutó. Temás reconditos que se habían ripeado de un CD como `track 3.mp3`, fueron buscados [en lo lugares más reconditos](https://web.archive.org/web/20051026143257/http://www.affair.de/) y ahora son `Affair - Day by Day.mp3`. Sus archivos fueron analizados y editados infinidad de veces por programas como [Mp3Tag](https://www.mp3tag.de/en/) entre otros que fueron aplicando distintos tipos de TOCs sin sentido, que más adelante dolerian remediar, como eliminar Album y Art Work de la metadata y reemplazarlo por imágenes custom que iban bien con el color del teclado de mi [Sony Ericsson Z310A](https://www.att.com/support_static_files/manuals/Sony_Ericsson_Z310a.pdf). Qué pajero, por dios! 

## El Pipeline

Hoy día, con servicios como Spotify, nada de eso tiene sentido, pero el alma presente del gordo archivero sigue vigente. La carpeta sigue estando y mi idea es contar un poco como hago para organizarla, stremearla y consumirla. A partir de ahora, el post se trata de explicar un poco la imágen de abajo.

![](/images/posts/music_pipeline.png)

Como fuente principal de música uso distintos servicios pero principalmente [Deezer](https://www.deezer.com/), sobre todo porque me permite bajar desde un album completo, hasta canciones individuales. Su mayor bondad es la calidad FLAC, pero no es lo único. Tiene la ventaja, aunque Deezer así no lo considere, de tener un compañero de software libre que permite la descarga de su música y demás metadata. [Deemix](https://dee-mix.one/en/download/), no solo descarga la múica si no mucha metadata ayuda con la categorización. Además, como mucho del contenido que existe en Deezer está en Spotify, te permite descargar playlists enteras que existen en Spotify, solamente con el link. Lo q resulta conveniente cuando te comparten música tu amigos normies. 

![](/images/posts/tags.png)

Ademas de Deemix, también uso [Lidarr](https://lidarr.audio/) para cuando quiero bajar un album entero. Pero el exito del mismo depende mucho de que tracker de torrents tengas configurado. Tengo pendiente meterme en [Red](https://interviewfor.red/en/index.html) y sondear que tanto mejor es comparado con lo que tengo ahora.

### Che, que está sonando? 

Para evitar la eterna busqueda en Google de nuestro querido tema `Day by Day` escrito por `Affair`, hoy en día, nos podemos apoyar en la base de datos abierta de [Music Brainz](https://musicbrainz.org/) . Y esto es lo que hacen [Beets](https://beets.io/?trk=public_post-text) y [Picard](https://picard.musicbrainz.org/), q son algo así como un Shazam + MP3Tag. Ambos sirven para organizar y reconocer canciones pero son fundamentalmente distintos. Ambos etiquetan la metadata de tus archivos de música basandose en diversos parámetros para identifidcarla. El método qué más me funciona es el [Accoustic ID](https://acoustid.org/) que basicamente es un identificador único (fingerprint) basado en la data del archivo. La manera en la que se calcula este id es inquietante. No es el objetivo de este post, pero basicamente es un conjunto de distintos algoritmos que tienen en cuenta desde los cambios en las frecuencias, principalmente entre los 12 semitonos existentes sin contar las distintas octavas (i.e C, C#, D, D#, E, F, F#, G, G#, A, A#, B) hasta el cálculo de [Mel-Frequency Cepstral Coefficients](https://www.youtube.com/watch?v=4_SH2nfbQZ8), que si te gustó Cálculo III probablemente lo encuentres interesante. Tanto en Picard como Beets, podés confugurar como querés organizar tu música. 

### Y dónde va Miranda ft. Tchaikovski EP? 

Por un lado, Beets mantiene una bliblioteca de tu musica en una base de datos y puede correr como daemon importando tu música a medida que la vas agregando. En mi opinión, tiene más hype del que se merece y usarlo para categorizar una biblioteca grande desde zero, puede ser tedioso. Está un poco flojo de documentación por lo que recomiendo directamente mirar (este repo) [https://github.com/florib779/beets-config/blob/master/config.yaml] para configurarlo. Picard, en cabmio es una app de escritorio con su propia UI. La encuentro útil para una primera pasada si no tenés tu música catgorizada. Ambas pueden mover archivos en la estructura que quieras. En mi caso uso el default de Picard, que también lo tengo seteado en Deemix. Que simplificado es `%albumartist%/ %album%/ %tracknumber%. %title%` pero puede ponerse más complejo, por ejemplo: 

```
$if2(%albumartist%,%artist%)/
$if(%albumartist%,%album%/,)
$if($gt(%totaldiscs%,1),$if($gt(%totaldiscs%,9),$num(%discnumber%,2),%discnumber%)-,)$if($and(%albumartist%,%tracknumber%),$num(%tracknumber%,2) ,)$if(%_multiartist%,%artist% - ,)%title%
```

Abmos programas usan diversos servicios para popular la metadata. Desde distintos géneros, el arte del album o la fotos del artista hasta las letras de las canciones. Si pensas embarcarte en esta aventura, te dejo una lista de las páginas en las que te tenés que crear una cuenta y sacar la API Key.

- https://musicbrainz.org/
- https://www.last.fm/
- https://fanart.tv/
- https://www.discogs.com/

### Backups

Al día de hoy, el archivo de música cuenta con `6446 carpetas` y `15627 archivos` contando `.mp3` , `.flac` y `.lrc` entre alguna que otra tapa de album  en `.jpg` . En beets, no está todo porque el proceso de categorización es un poco tedioso. El creador tiene un approach interesante al respecto: [An Apology and a Brief Interlude](https://beets.readthedocs.io/en/stable/guides/tagger.html#an-apology-and-a-brief-interlude).

![](/images/posts/music_files.png)

Para hacer backups, uso `rsync`, claro está. Te dejo el one-liner por si te interesa:

```shell
rsync -avr --delete --ignore-existing --info=progress2 /mnt/HDD2/music/ media-bkp/music
```

### Off-line es el nuevo lujo

Tngencial al hecho de almacernar y hostear mi música (`r/musichoarder`), está en el mundo de `/r/audiophile` y como un equipo de audio no es algo que pueda siquiera considerar por ahora, opté por un `r/DigitalAudioPlayer`. No soy un obsesivo de la calidad pero si me siento identificado con ese goce que existe en el indagar a fondo un tema especifico, sobre todo cuando ese tema es bien de nicho y está bien alejado del mainstream. Como dijo [**Brawntuhsaur**](https://www.reddit.com/user/Brawntuhsaur/)

> The essence of every single hobby is to take minuscule differences and blow them way out of proportion and spend money to experience those differences that you’ve convinced yourself are huge differences.
> 
> _(_[_ref. to the comment_](https://www.reddit.com/r/audiophile/comments/1l4vdnl/comment/mwc1duf/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)_)_

Después de investigar, mayormente por Reddit y otros antros, opté comprar un iPod Classic modeado. No voy a entrar en detalle de que se necesita para modear un iPod pero en caso de que alguno quiera hacerlo, les dejo un link dónde pueden comrpar lo necesario para armar un iPod que cumpla los estandares esperados de un DAP en 2025. El principal motivo por el cual opté por un iPod, fue [RockBox](http://rockbox.org), un remplazo del OS que trae de stock, que te permite reproducir `.flac`, correr scripts en lua y customizarlo con una oferta [gigante de temas.](https://themes.rockbox.org/index.php?target=ipod6g)

Si estás penando comprar un DAP, dejame decirte que existen muchas mejores opciones, que reproducen en mejor calidad y más baratos, pero en su mayoría todas traen Android o un mod del mismo.

![](/images/posts/ipod.png)

Para sobrellevar lo poco prático que resulta tener que cargarle música, armé una suerte de automatizaciones que paso a compartirles. Basicamente, la solución consta de dos `Unit Files` que se accionan consecutivamente después de conectar el iPod al servidor dónde tengo almacenada la música. Automaticamente, se monta el dispositivo y corre un script que sincorniza tanto la música, como las playlist.

```nix
  systemd.services."ipod" = {
    description = "Sync files from iPod when mounted";
    wantedBy = [ "mnt-ipod.mount" ]; # Trigger after the mount is ready
    after = [ "mnt-ipod.mount" ];
    serviceConfig = {
      Type = "oneshot";
      ExecStart = ''/home/canus/scripts/nixos/sync-ipod.sh'';
      User = "contre"; # Ensure this user has access
    };
  };

  # Mount unit for the iPod
  systemd.mounts = [
    {
      what = "/dev/disk/by-uuid/8722-166E"; # UUID for /dev/sdf1
      where = "/mnt/ipod"; # Mount point
      type = "vfat"; # Filesystem type
      options = "nofail,x-systemd.automount,uid=1000,gid=100"; # Optional: adjust UID/GID
    }
  ];
```

```bash
#!/run/current-system/sw/bin/bash
# Capture rsync output (stdout + stderr) and preserve line breaks
CONVERT=$(
# Needed for special characters in FAT32 file systems 
  /home/contre/.nix-profile/bin/convmv -r -f utf-8 -t utf-8 --nfc --notest /mnt/HDD2/music/ 2>&1
)
RSYNC=$(
  /home/contre/.nix-profile/bin/rsync -avr --ignore-existing --include='*' --stats --exclude='*.lrc' /mnt/HDD2/music/ /mnt/ipod/Music/ 2>&1 |
  sed '0,/^$/d' | grep -e "Number of" -e "File list size"
)
/home/contre/.nix-profile/bin/rsync -av --inplace --info=progress2 /mnt/SSD/config/emby/data/userplaylists/ /mnt/ipod/Playlists 2>&1

# Send via curl using --data-urlencode to preserve formatting
/run/current-system/sw/bin/curl -s -X POST --data "iPod Sync ✅ --- $CONVERT - $RSYNC" https://ntfy.server.home/all
```

En cuanto termina de syncronizar, me notifica. No suele tardar más de 5 segundos depende que tanta música haya descargado. Para las playlist, las armó en Symfonium y las syncronizo con Emby. De esa forma, cuando el script corre toma las playlist de ahí. Así, tengo syncronizado playslists y música en Emby (Web y TV), IPod (Offline), y Celular (Symfonium). El formato común que usan es `.m3u` , que consta simplemente de una lista de los _paths_ dónde viven esos archivos.

Si bien todo esto puede sonar anacrónico, no intento que se lea como un acto de rebeldía contra el consumo mainstream de música. Creo, sinceramente, que hay valor en coleccionar, clasificar y cuidar. Son actos inherentes a la soberanía personal, de los cuales uno no puede desligarse tan fácilmente si pretende conservarla. Sea en el campo que sea, es una práctica necesaria e inevitable: una lucha que, gracias al trabajo de muchos, hoy se puede elegir pelear.. o relegar.

## Que sigue ?

Hace ya unos meses vengo usando este flujo y no puedo quejarme. Funciona bastante bien. Mi proximo objetivo sería meterme a RED pero la entrevista me [parece bastante tediosa](https://interviewfor.red/en/index.html). Quisiera saber si realmente vale la pena. Estoy conociendo mucha música que no se consigue en buena calidad y no está en los medios populares de streaming como Spotify o Deezer, todo indica q hay mucho en Vinilo. También quisiera configurar [Pinchflat](https://github.com/kieraneglin/pinchflat), para descargar música de Youtube. El problema es catalogar los enganchados en un solo video, y al no poder desacargar música losless, tampoco siento que valga tanto la pena. También me gustaría implementar algo como [Sonic Analysis](https://support.plex.tv/articles/sonic-analysis-music/) para crear playlist. Por ahora, lo único que vi que lo soporta es Plex y no encontré alternativa. Symfonium tiene una feature que se integra muy bien, pero necesitás Plex como backend indefectiblemente. Por último, me gustaría algo q me recomiende canciones para agregar a una _Playlist_ existente.

Hay un montón de cosas que encontré y descubrí que no da la vida para reflejar en este blog post. Pero te dejo algunos links, que si estás en estas, te pueden llegar a servir.

- https://bandcamp.com/
- https://www.discogs.com/
- https://musicbrainz.org/
- https://www.albumoftheyear.org
- https://github.com/krateng/maloja
- https://www.last.fm/
- [https://www.deezer.com/](https://www.deezer.com/us/offers)
- https://dee-mix.one/en/download/
- https://github.com/acoustid/chromaprint/
- https://beets.io
- https://fanart.tv/
- https://github.com/florib779/beets-config/blob/master/config.yaml
- https://www.reddit.com/r/DigitalAudioPlayer/
- https://www.reddit.com/r/musichoarder/
- https://www.reddit.com/r/audiophile/
- https://www.reddit.com/r/LetsTalkMusic/
- https://www.reddit.com/r/Beatmatch/
- https://support.plex.tv/articles/sonic-analysis-music/
- https://rateyourmusic.com
- https://www.metal-archives.com/
- https://suno.com/explore/
- https://bndcmpr.co/
