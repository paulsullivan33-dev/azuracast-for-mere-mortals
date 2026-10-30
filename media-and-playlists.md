# Media and Playlists

A station is its library and the rules for playing it.
This chapter covers both halves: getting music into
AzuraCast in an organised way, and the playlist types that
decide how the AutoDJ draws from it. By the end, your
station will be playing music continuously — the moment
AzuraCast starts to feel like a radio station.

## Getting music in

AzuraCast keeps each station's media in its own storage
location. There are three doors in:

- **The web page.** The station's **Media** page accepts
  uploads from the browser, one file or many, and lets you
  organise them into folders. Fine for adding this week's
  songs.
- **SFTP.** For a whole library, use the SFTP service on
  port **2022** with any SFTP client, signing in with the
  credentials the Media page shows you. The files land in
  the same storage the web page sees.
- **A folder of your own.** An administrator can point a
  station's storage at an S3-compatible bucket, or mount a
  host directory into the container (the Docker chapter of
  the project's documentation gives the override recipe).
  Most home stations never need either.

However the files arrive, AzuraCast reads each one's
**tags** — title, artist, album, length — and shows them in
the media manager, where you can edit tags, preview a file
in the browser, and search the library. The AutoDJ announces
songs from these tags, so a library with tidy tags produces
tidy "now playing" displays everywhere downstream. Files
whose names are meaningful but whose tags are empty are the
most common cause of a station that announces "unknown
artist" all day; fix the tags, in AzuraCast or in your
favourite tag editor before uploading.

One more idea with a long shadow: **duplicate detection
and repetition rules live downstream of the library**, in
the scheduler that picks each next song — it avoids
replaying the same song too soon and can keep the same
artist from following itself. Those rules work best in a
library with some depth. Twelve songs on shuffle is a
jukebox having a breakdown; a few hundred is a station.

## Playlists: piles with rules

Uploading music does **not** put it on air. A song reaches
listeners only through a **playlist**: a named pile of
songs (or a whole folder of them) plus rules for when the
AutoDJ may draw from it. This is the single most common
beginner confusion — music uploaded, station silent —
because the step "add the songs to a playlist" was skipped.
Create at least one playlist, put songs in it, and the
station has something to say.

In the media manager you assign songs to playlists; on the
station's **Playlists** page you create and tune the
playlists themselves. Each playlist has a **type**, and the
type is the rule:

- **General Rotation.** The standard pile. Its songs are
  always eligible, mixed with the songs of your other
  rotation playlists. Each rotation playlist has a
  **weight** (a number, defaulting to 3): over time, a
  playlist with weight 6 is drawn from about twice as often
  as one with weight 3. Use weights to make new music
  prominent and old favourites occasional without moving
  files around.
- **Once per X Songs.** After every X songs from elsewhere,
  the AutoDJ inserts one song from this playlist. The home
  of jingles, station idents and adverts: a two-minute
  announcement should not compete in general rotation.
- **Once per X Minutes.** The same idea on a clock instead
  of a song count.
- **Once per Hour.** One song from this playlist in each
  hour — a news bulletin, a hourly ident, a time check.
- **Advanced.** A hand-written Liquidsoap expression, for
  people who have outgrown the form. Chapter 12's advice
  applies: know that you are opting out of the guardrails.

Within a playlist, **playback order** decides how its songs
are picked: **shuffled** (the pile is shuffled and played
through in that order, then reshuffled), **random** (a
fresh coin flip each time — can repeat and can starve), or
**sequential** (the order you arranged, for albums and
programmed shows). Shuffled is the right default for
rotation; random is rarely what people who choose it
actually want.

Playlists can also be **groups**: one playlist made of
other playlists, played through in a defined pattern — the
traditional radio "clockwheel" (two songs, an ident, three
songs, the news). Grouped playlists arrived in version
0.24.0 and are the tool for stations that think in hours
rather than in piles. Ordinary stations can ignore them
happily.

## Remote streams as playlists

A playlist's source does not have to be files. A playlist
can instead carry a **remote stream URL** — another
station's stream, a syndicated show — which the AutoDJ
plays when the playlist is active. Because a stream has no
natural end, these playlists **must be scheduled** (the
next chapter): the schedule is what tells the AutoDJ when
to join the remote programme and when to leave it.

## Requests

If you enable them for a station, listeners can **request**
songs from a public page (and via the API), and the
scheduler slots requests into the rotation. Requests are a
delight and a small chaos engine in equal measure; the
settings let you set how often one listener may ask, and
0.24.0 added request-queue playlists for stations that want
requests played only at chosen moments. Start with requests
off, turn them on when the station is otherwise settled,
and keep the delay rules — a request played instantly is a
station anyone can steer.

## The check

Your station is set up when: the media manager shows your
library with sane tags, at least one General Rotation
playlist contains a healthy pile of songs, and the station
profile shows the AutoDJ running and the "now playing"
display cycling through real songs. Silence at this point
is almost always one of three things — no playlist, an
empty playlist, or the AutoDJ service stopped — and
chapter 13 takes them in that order.
