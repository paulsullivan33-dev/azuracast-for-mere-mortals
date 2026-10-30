# The Moving Parts

AzuraCast looks like one program and is really a small
crew, each member doing one job. This chapter names the
crew and follows a single song on its journey from your
disk to a listener's phone, so that every later chapter's
settings have somewhere to live.

## The manager: the AzuraCast web application

The part you see is a web application (written in PHP, in
case you ever read its logs). It stores everything in a
**MariaDB** database: your stations, users, playlists,
schedules, settings and statistics. When you change a
setting in the web page, the application does two things:
it remembers the change, and it *rewrites the configuration
files* of the radio programs below and tells them to pick
the new configuration up. You almost never edit those
files yourself. That indirection is AzuraCast's whole
value — and its one great caveat, which chapter 12 states
plainly: the generated configuration is the product, and
fighting it by hand-editing files is a losing game.

## The AutoDJ: Liquidsoap

**Liquidsoap** is a scripting language and engine for
audio streaming, and in AzuraCast it is the **backend** —
the AutoDJ. It holds your playlists as rules: *choose from
this pile most of the time, drop one song from that pile
every hour, switch to this other pile between 6 and 10 in
the morning*. Left alone, it assembles a continuous,
crossfaded stream from those rules forever. It also stands
ready to accept a live DJ: a DJ's program connects to a
listening socket Liquidsoap holds open (the *harbor*), and
Liquidsoap blends the live input over the automatic
programme and back again when the DJ disconnects.

Liquidsoap asks your AzuraCast application what to play:
for each next song, AzuraCast's own scheduler picks from
the playlists whose rules say *now*, which is how features
like listener requests and "avoid repeating the same song
twice in a row" are enforced by the manager rather than
the player.

## The broadcaster: Icecast

**Icecast** is the **frontend** — the program listeners
actually connect to. It is deliberately simple and very
good at one thing: taking a single incoming stream and
serving copies of it to a great many listeners at once,
cheaply. It calls each stream it serves a **mount point**,
named like a file path: a station's main stream is
typically `/radio.mp3`.

Two other frontends exist and are worth knowing by name.
**Shoutcast 2** is a commercial broadcaster some listener
directories expect; on 64-bit Intel/AMD machines AzuraCast
can install it for you after the main installation (it is
not free software and does not come bundled). **RSAS**
(Rocket Streaming Audio Server) is another commercial
alternative. This guide follows the default, Icecast,
throughout.

## The supporting crew

Inside the same container, four more services do quiet
work:

- **NGINX**, the web server, serves the AzuraCast pages —
  and also acts as a **proxy for the radio streams**, so
  listeners can reach a stream through the ordinary web
  ports (80 and 443) instead of the radio ports in the
  8000s, which some networks block. Chapter 9 explains why
  that matters.
- **Redis** is a fast memory store used for sessions, a
  message queue, and caching.
- **Centrifugo** pushes live "now playing" updates to web
  pages, so the player on your station's public page changes
  the song title the moment the song changes, without
  reloading.
- **SFTPGo** provides the SFTP file service (port 2022 in
  the default install) for uploading music in bulk.

## How it is all packaged

Everything above runs in **one Docker container** in the
current design — application, database, caches and radio
programs together — alongside one small helper container,
the **updater**, which watches for newer AzuraCast images
and applies them. Your data lives in named storage volumes
attached to that container: one for station files and
music, one for the database, one for backups, and a few
smaller ones for extras like certificates. Chapter 4 walks
through what the installer sets up, file by file.

## One song's journey

Put together, a song's trip looks like this:

1. You upload the file; AzuraCast stores it in the
   station's storage and reads its tags — title, artist,
   album, length.
2. You place it in a playlist. The playlist's rules say
   when songs from it may be chosen.
3. When the current song nears its end, Liquidsoap asks
   AzuraCast for the next one; the scheduler applies the
   rules and answers with a file.
4. Liquidsoap plays it, crossfading from the previous
   song, and sends the single resulting stream to Icecast.
5. Icecast serves that stream, at the station's mount
   point, to every connected listener — directly on the
   station's radio port, or through the NGINX proxy on the
   web ports.
6. Along the way AzuraCast notes what played and how many
   were listening, which becomes the statistics and reports
   of chapter 9.

When a DJ connects, step 3 changes: Liquidsoap fades the
AutoDJ out, the DJ's own programme supplies the audio for
as long as they stay connected, and step 6 notes who was
live. Chapter 8 covers that handover in detail.
