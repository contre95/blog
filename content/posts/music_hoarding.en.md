---
title: Music Gardening
date: 2025-06-12  00:18:54
tags:
    - Music
    - Tech
    - Self-Hosted
---

![Intro](/images/posts/music_banner.png)

It must have been around 2009 when I asked my older cousin what kind of music she listened to. That was the first time I clearly associated the idea of a "band" with "song". Up until then, my relationship with music was more through osmosis than personal choice. Later on, I’d come to realize that what I had been listening to was mostly Anglo rock, some blues and some jazz —basically international stuff, kind of disconnected from any local context. I mostly listened to Aspen, a radio station that played (and still plays) soft rock classics from the 80s and 90s, along with a few English ballads and some pop. My dad was the one in charge of the dial, and that shaped a big part of what came into the house: lots of Genesis, Phil Collins, Pink Floyd, Eric Clapton, and other slightly soft-boy bands like Norah Jones, Bossa Nova, and so on, stuff I actually struggle to listen to nowadays. Argentine music came in more through Sumo, some La Bersuit, Soda Sterel and Serú Girán. But it was my friends who introduced me to more contemporary national rock—bands like Árbol, Airbag, and Ataque 77, which played alongside the cumbia and reggaeton hits of the time, like Daddy Yankee, Wisin & Yandel, and other Puerto Rican music.

It was when I got my first computer that I really started choosing what to listen to. My music folder became my first big archive: it was carefully curated, with no room for full albums or songs I didn’t like. The first wave of music came from my memory and from [Ares Galaxy](https://es.wikipedia.org/wiki/Ares_Galaxy). That’s where I downloaded the bands I mentioned earlier. The ones that took the longest to arrive were the songs I used to hear on the radio, those I didn’t know the names of. Many came back nameless, hidden in USB sticks we used to share among friends. I’d only realize if they were familiar once I played them. Later on, apps like [Sound Hound](https://play.google.com/store/apps/details?id=com.melodis.midomiMusicIdentifier.freemium&hl=en-US&pli=1) or [Shazam](https://www.shazam.com/) helped me categorize them.

Since then, up to today, that same folder has gone through several iterations, not only in variety and quality of content but also in its categorization and organization. It was never accidentally deleted, never lost, and never died; it just mutated. Obscure tracks which were ripped from a CD such as `track 3.mp3`, were tracked down [in the most hidden places](https://web.archive.org/web/20051026143257/http://www.affair.de/) and are now `08 Day by Day.mp3` by `Affair` a german band that made 80s like soft rock and still maiktains their flash made website until nowadays ([affair.de](affair.de)). The files have been analyzed and edited countless times by programs like [Mp3Tag](https://www.mp3tag.de/en/) among others, gone through all sorts of ridiculous TOCs that later caused headaches to fix. Things such as removing Album and Artwork from all songs and replacing them with custom images that matched the color of my [Sony Ericsson Z310A](https://www.att.com/support_static_files/manuals/Sony_Ericsson_Z310a.pdf) keyboard. What a pain, seriously!

## The Pipeline

Nowadays, with services like Spotify, none of that really makes sense anymore, but the soul of that geek hoarder is still alive. The folder is still there, and my idea is to share a bit about how I organize it, stream it, and consume it. From now on, this post is about explaining a bit the image below.

![](/images/posts/music_pipeline.png)

As my main source of music, I use various services but mostly [Deezer](https://www.deezer.com/), mainly because it lets me download everything from full albums to individual songs. Its biggest strength is FLAC quality, but that’s not all. It has the advantage—though Deezer itself doesn’t acknowledge it—of having a free software companion that allows downloading its music and other metadata. [Deemix](https://dee-mix.one/en/download/) not only downloads the music but also lyrics and other metadata that helps with categorization. Plus, since much of Deezer’s content is also on Spotify, it lets you download entire playlists from Spotify just by using the link. Which is super convenient when your normie friends share music with you.

![](/images/posts/tags.png)

Besides Deemix, I also use [Lidarr](https://lidarr.audio/) when I want to download a whole album. But its success really depends a lot on which torrent tracker you have configured. I still plan to check out [Red](https://interviewfor.red/en/index.html) and see how much better it is compared to what I have now.

### Hey, what's playing ? 

To avoid the endless Google search for our beloved track Day by Day by Affair, nowadays we can rely on the open database [Music Brainz](https://musicbrainz.org/). This is what [Beets](https://beets.io/?trk=public_post-text) and [Picard](https://picard.musicbrainz.org/) do —they’re kind of like Shazam + MP3Tag. Both help organize and identify songs, but they are fundamentally different. Both tag the metadata of your music files based on various parameters to identify them. The method that works best for me is [Acoustic ID](https://acoustid.org/), which is basically a unique identifier (fingerprint) based on the data in the file. The way this ID is calculated is fascinating. It’s not the goal of this post to explain it in detail, but basically it’s a set of different algorithms that consider everything from frequency changes, mainly between the 12 semitones (not counting the octaves) (i.e. C, C#, D, D#, E, F, F#, G, G#, A, A#, B) to the calculation of [Mel-Frequency Cepstral Coefficients](https://www.youtube.com/watch?v=4_SH2nfbQZ8), which if you liked Calculus III, you’ll probably find interesting. Both Picard and Beets let you configure how you want to organize your music.

### Where do I place Tchaikovski ft. The Plasmatics EP? 

On one hand, Beets keeps a library of your music in a database and can run as a daemon, importing music as you add it. In my opinion, it’s a bit overhyped, and using it to categorize a large library from scratch can be tedious. The documentation is a bit lacking, so I recommend just checking out [this repo](https://github.com/florib779/beets-config/blob/master/config.yaml) for configuration. Picard, on the other hand, is a desktop app with its own UI. I find it useful for a first iteration if your music isn’t categorized yet. Both can move files into whatever folder structure you want. In my case, I use Picard’s default, which I’ve also set in Deemix. The simplified version is:

`%albumartist%/ %album%/ %tracknumber% %title%`

But it can definitely get more complex, for example:

```
$if2(%albumartist%,%artist%)/
$if(%albumartist%,%album%/,)
$if($gt(%totaldiscs%,1),$if($gt(%totaldiscs%,9),$num(%discnumber%,2),%discnumber%)-,)$if($and(%albumartist%,%tracknumber%),$num(%tracknumber%,2) ,)$if(%_multiartist%,%artist% - ,)%title%
```

Both programs use a variety of services to populate metadata - everything from genres, album art, and artist photos to song lyrics. If you're thinking of diving into this adventure, here's a list of websites where you'll need to create an account and get an API key: 

- https://musicbrainz.org/
- https://www.last.fm/
- https://fanart.tv/
- https://www.discogs.com/

### Backups

As of today, the music archive has `6,446 folders` and `15,627 files`, including `.mp3`, `.flac`, and `.lrc` —plus the occasional album cover in `.jpg`. Not everything is in Beets, since the categorization process can be a bit tedious. The creator actually has an interesting take on that: [An Apology and a Brief Interlude](https://beets.readthedocs.io/en/stable/guides/tagger.html#an-apology-and-a-brief-interlude).

![](/images/posts/music_files.png)

For backuos, I use `rsync`, what else? Here's the one-liner if you are interested: 

```shell
rsync -avr --delete --ignore-existing --info=progress2 /mnt/HDD2/music/ media-bkp/music
```

### Off-line is the new luxury

Tangent to the whole music storing and self-hosting thing ([r/musichoarder](https://www.reddit.com/r/musichoarder)), there’s also the world of [r/audiophile](https://www.reddit.com/r/audiophile), and since a proper audio setup is not something I can even consider right now, I leaned toward buying a small  [r/DigitalAudioPlayer](https://www.reddit.com/r/DigitalAudioPlayer). I’m not obsessed with quality, but I definitely relate to that deep satisfaction that comes from digging into a specific topic, especially when it’s niche and far from the mainstream. In the words of [Brawntuhsaur](https://www.reddit.com/user/Brawntuhsaur/)

> The essence of every single hobby is to take minuscule differences and blow them way out of proportion and spend money to experience those differences that you’ve convinced yourself are huge differences.
> 
> _(_[_ref. to the comment_](https://www.reddit.com/r/audiophile/comments/1l4vdnl/comment/mwc1duf/?utm_source=share&utm_medium=web3x&utm_name=web3xcss&utm_term=1&utm_content=share_button)_)_

After doing some research —mostly through Reddit and other shady corners of the internet— I ended up buying a modded iPod Classic. I’m not going to go into detail about what you need to mod an iPod, but in case anyone wants to give it a shot, here’s a link where you can get everything you need to build an iPod that meets the standards expected of a DAP in 2025. The main reason I went with an iPod was [RockBox](http://rockbox.org), a replacement for the stock OS that lets you play `.flac` files, run Lua scripts, and customize it with a [huge selection of themes](https://themes.rockbox.org/index.php?target=ipod6g). And yes, it also runs Doom. 

If you’re thinking about buying a DAP, let me just say there are way better options out there, with better sound quality and at lower prices. But most of them run Android, or some modded version of it.

![](/images/posts/ipod.png)

To deal with how impractical it can be to manually load music onto it, I put together a sort of automation setup that I’ll share here. Basically, the solution involves two `Unit Files` (in Nix) that run consecutively after the iPod is connected to the server (where my music is stored). The device gets mounted automatically, and then a script runs that syncs both the music and the playlists.

```nix
  systemd.services."ipod" = {
    description = "Sync files from iPod when mounted";
    wantedBy = [ "mnt-ipod.mount" ]; 
    after = [ "mnt-ipod.mount" ];
    serviceConfig = {
      Type = "oneshot";
      ExecStart = ''/home/canus/scripts/nixos/sync-ipod.sh'';
      User = "contre";
    };
  };

  # Mount unit for the iPod
  systemd.mounts = [
    {
      what = "/dev/disk/by-uuid/8734-1678";
      where = "/mnt/ipod";
      type = "vfat";
      options = "nofail,x-systemd.automount,uid=1000,gid=100";
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

Once the sync is done, I get a notification. It usually doesn’t take more than 5 seconds, depending on how much music was downloaded since last sync. For playlists, I build them in Symfonium and sync them with Emby. That way, when the script runs, it pulls the latest version of the playlists from there. So, I have playlists and music synced across Emby (Web and TV), iPod (Offline), and my phone (Symfonium). The common format they use is `.m3u`, which is basically just a list of file paths.

Even if all this sounds a bit anachronistic, I don’t mean for it to come off as some rebellious act against mainstream music consumption. It's more about my belief thats there’s value in collecting, organizing, and preserving. These are acts tied to personal and cognitive sovereignty, acts you can’t easily detach from if you intend to hold on to it. No matter the field, it’s a necessary and inevitable practice: a fight that, thanks to the work of many, you now get to choose—whether to take it on, or let it go. I have personally decided to fight it in an essential common field for all humans: music. 

## What’s next?

I’ve been using this flow for a few months now and honestly, I can’t complain. It works really well. My next goal would be to get into RED, but the interview [feels pretty tedious](https://interviewfor.red/en/index.html). I’d really like to know if it’s worth it. I’m discovering a lot of music that’s unavailable in good quality and missing from mainstream streaming platforms like Spotify or Deezer. Everything points to vinyl being the source for a lot of it.

I’d also like to set up [Pinchflat](https://github.com/kieraneglin/pinchflat) to download music from YouTube. The issue is properly cataloging mixes on continuous videos, and without the ability to download lossless audio, I’m not sure it’s really worth the effort. I’d also love to implement something like [Sonic Analysis](https://support.plex.tv/articles/sonic-analysis-music/) to create playlists. So far, the only platform I’ve seen that supports it is Plex, and I haven’t found a real alternative. Symfonium has a feature that integrates really well, but you must use Plex as the backend. Finally, I’d like a tool that recommends tracks to add to an existing playlist.

There’s a ton of stuff I’ve found and explored that didn’t fit into this blog post. But here are a few links that might come in handy if you’re into this kind of thing.

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
