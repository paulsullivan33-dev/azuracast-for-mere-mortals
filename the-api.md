# The API

AzuraCast's web pages are a front end. Underneath them is
a documented **REST API** — the same interface the pages
themselves use — and it is the part of AzuraCast that other
software builds on: player widgets, phone apps, "now
playing" signs, home-automation panels. This chapter reads
the API's most-used endpoint in full, then covers the keys
that unlock the rest.

Everything here is grounded in the **OpenAPI specification
AzuraCast publishes**: a machine-readable description of
every endpoint, shipped in the project's repository
(`web/static/openapi.yml`, over eleven thousand lines at
version 0.24.0) and served by every installation at
`GET /api/openapi.yml`. The wider API documentation, with
an interactive explorer, is linked from the project's site.
If a claim in this chapter ever disagrees with the spec
your installation serves, believe the spec: it is
generated from the running code.

## The public endpoints: no key required

A small set of endpoints answers without any credentials
(they are marked `security: []` in the spec):

- `GET /api/status` and `GET /api/time` — is it up, and
  what does it think the time is.
- `GET /api/stations` — the installation's stations.
- `GET /api/nowplaying` — every station's current state.
- `GET /api/nowplaying/<station_id>` — one station's.

`nowplaying` is the famous one: it is how every external
player knows what your station is doing.

## The now-playing response, read in full

The spec's `Api_NowPlaying` object has eight parts. Here
is a response for one station, with the values invented
but every field name and shape taken from the
specification — treat it as a map, not a photograph:

```json
{
  "station": {
    "id": 1,
    "name": "Example FM",
    "shortcode": "example_fm",
    "listen_url": "https://radio.example.com/listen/example_fm/radio.mp3",
    "public_player_url": "https://radio.example.com/public/example_fm",
    "mounts": [ ],
    "remotes": [ ]
  },
  "listeners": { "total": 20, "unique": 15, "current": 20 },
  "live": {
    "is_live": false,
    "streamer_name": "",
    "broadcast_start": null
  },
  "now_playing": {
    "played_at": 1700000000,
    "duration": 203,
    "playlist": "General Rotation",
    "is_request": false,
    "song": {
      "title": "An Example Song",
      "artist": "The Examples",
      "album": "Greatest Hits"
    },
    "elapsed": 25,
    "remaining": 178
  },
  "playing_next": { "song": { "title": "The Next Song" } },
  "song_history": [ ],
  "is_online": true,
  "cache": "hit"
}
```

Reading it top to bottom: **station** identifies the
station and carries the addresses chapter 9 introduced
(`listen_url`, `public_player_url`, plus playlist-file
URLs for external players). **listeners** counts the
audience three ways — total connections, unique listeners,
and a legacy duplicate of total kept for older clients.
**live** is chapter 8 in data form: whether a DJ is on
air, their display name, and when they started.
**now_playing** is the current song with its playlist of
origin, whether a listener requested it, and — the fields
every progress bar is built from — **elapsed** and
**remaining** seconds. **playing_next** peeks at the
AutoDJ's queue, **song_history** lists what played before,
**is_online** says whether the stream itself is up, and
**cache** admits where the answer came from (`hit`,
`database` or `station`), a small honesty most APIs would
not bother with.

Two practical notes. The endpoint is **cached for a few
seconds** (the route caches for 15 seconds), so poll it
politely — every 15–30 seconds is plenty for a player, and
a thousand widgets polling every second is a self-inflicted
denial of service. And for live updates without polling,
AzuraCast's Centrifugo service (chapter 2) pushes the same
data over a websocket feed; the API documentation
describes connecting to it.

## Asking with curl

Against your own installation, from any machine:

```bash
curl https://radio.example.com/api/nowplaying/1
```

No key, nothing to configure — which is also a fact to
think about once: your station's current song, audience
size and DJ state are public information by design. That
is how radio works (anyone can listen and count), but if
you run a private station, know that "private" applies to
the stream password, not to this feed.

## API keys: everything else

Every other endpoint — managing stations, media,
playlists, users, backups — requires an **API key**: a
secret string created per user under that user's account,
sent in the **`X-API-Key`** HTTP header. The key carries
*exactly its owner's permissions*: an administrator's key
can administer, a station manager's key can manage that
station, and a key from a read-only account can do no
damage beyond reading. Two rules follow:

- **One key per integration**, named for what it is for,
  so revoking the phone app does not break the studio
  sign.
- **Keys are passwords.** They travel in headers, not in
  URLs; they belong in the calling script's configuration,
  never in a public web page's JavaScript. The public
  now-playing endpoint needs no key precisely so that web
  players never have to hold one.

A worked keyed call — asking for the station's details as
an administrator's script might:

```bash
curl -H "X-API-Key: <your key>" \
     https://radio.example.com/api/admin/stations
```

## What people build

The pattern to copy is the one AzuraCast itself uses:
public data from the keyless endpoints for anything a
listener sees; keyed calls only from software you control,
for management and automation. Station signs that show the
current song, bots that announce a DJ going live,
schedulers that sync playlists from a spreadsheet — all of
them are this chapter and nothing more exotic.
