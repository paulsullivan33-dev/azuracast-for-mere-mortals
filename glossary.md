# Glossary

**AutoDJ** — AzuraCast's name for the station's automatic
programme: Liquidsoap, following your playlists and
schedules, playing music when no DJ is live. Also called
the station's *backend*.

**Backend / Frontend** — the two halves of a station. The
backend (Liquidsoap) *produces* the broadcast; the
frontend (Icecast, by default) *serves* it to listeners.

**Bitrate** — how much data per second a stream uses,
in kilobits per second (kbps). Higher is better sound and
more bandwidth per listener.

**Centrifugo** — the service inside AzuraCast that pushes
live "now playing" updates to web pages.

**Clockwheel** — radio slang for an hour built from a
repeating pattern of songs, idents and features; in
AzuraCast, a *grouped playlist*.

**DJ port** — the port a live DJ's encoder connects to:
the station's Icecast port plus five (8005 for the first
station).

**Encoder** — a program (BUTT, Mixxx, RadioBOSS) that
sends a DJ's live audio to the station.

**Harbor** — Liquidsoap's name for the listening socket
that accepts a live DJ's connection.

**Icecast** — the broadcasting server that serves one
stream to many listeners; AzuraCast's default frontend
(the Icecast-KH variant).

**Liquidsoap** — the audio-streaming language and engine
that acts as AzuraCast's AutoDJ.

**Mount point** — the path of a stream on the frontend,
such as `/radio.mp3`. One station can have several, at
different bitrates.

**Now Playing** — the live data feed (`/api/nowplaying`)
describing what a station is playing, who is live, and how
many are listening.

**Playlist** — a named pile of songs plus the rules
(rotation weight, once-per-hour insertions, schedule) for
when the AutoDJ may draw from it.

**Proxy (radio proxy)** — NGINX serving the streams over
the ordinary web ports (80/443) so listeners need not
connect to the 8000-series radio ports directly.

**Radio port** — one of the 8000-series ports assigned to
stations, in blocks of ten (base, base+5 for DJs, and so
on).

**Relay** — another server carrying your station's stream;
a *remote relay* in AzuraCast is either statistics-only or
a full relay it feeds itself.

**Release channel** — Rolling Release (`latest`) or Stable
(`stable` / a pinned version): how your installation
receives AzuraCast updates.

**Rolling release** — software published continuously as
changes are finished, rather than in numbered versions.

**Schedule item** — a time window attached to a playlist:
the hours and days during which the AutoDJ may use it.

**Shortcode** — a station's short, URL-safe identifier,
used in its public and listen addresses.

**Shoutcast** — a commercial broadcasting frontend
AzuraCast can install and drive as an alternative to
Icecast on 64-bit Intel/AMD machines.

**Station** — the unit of AzuraCast: one channel with its
own music, playlists, DJs, mounts and statistics. One
installation hosts many.

**Streamer account** — a DJ account: the username and
password an encoder uses to go live on a station.

**VPS** — a virtual private server: a rented slice of a
hosting company's machine, with its own public address and
openable ports.

**Web DJ** — AzuraCast's browser-based live broadcasting
page; a DJ account, a microphone and a browser tab are
the whole setup.
