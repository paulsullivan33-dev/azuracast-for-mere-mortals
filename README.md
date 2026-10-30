# AzuraCast for Mere Mortals

Running a radio station used to mean a stack of separate
programs, each with its own configuration file in its own
dialect: one to play the music, one to serve it to listeners,
one to switch between them when a DJ showed up. **AzuraCast**
is a single, self-hosted web application that installs that
whole stack for you and runs it from one web page. You upload
music, arrange it into playlists, and the station plays. When
nobody is at the microphone, an automatic DJ keeps the music
going around the clock; when a real DJ connects, the station
hands over to them and takes the stream back when they leave.

AzuraCast is installed with **Docker** on a machine you
control — most often a small rented server (a VPS), but a
spare home computer works too. Everything runs inside one
container that AzuraCast's installer downloads and sets up:
the web interface, the database, and the radio software
itself. The names underneath matter, and this guide names
them honestly: **Liquidsoap** is the automatic DJ that
decides what plays, and **Icecast** is the broadcasting
server your listeners actually connect to. AzuraCast is the
manager standing in front of both, and knowing which piece
does what turns most confusion into a named problem.

One honest note before anything else, because it shapes the
whole guide: **this guide's lab had no machine able to run
AzuraCast.** AzuraCast's only supported installation uses
Docker containers, and the lab machine could not run
containers at all. So instead of pretending, the lab took
the other honest route: it read AzuraCast's own files. The
installer script, the Docker Compose file it delivers, the
environment settings, the port-assignment code, and the
published API specification were all dissected directly from
the project's repository (version **0.24.0**, October 2026),
and the workflows were checked against the project's official
documentation. Every concrete number in this guide — every
port, every default — comes from those files, and
`criteria.md` keeps the full ledger of what was read and
where.

And one honest paragraph that belongs at the front of any
web-radio guide, not buried at the back: **playing music to
the public is a licensed activity in most countries.** If
your station can be heard by anyone beyond your own
household, the composers and performers of the music are
usually owed royalties, collected through licensing bodies
whose names vary by country. AzuraCast cannot license music
for you, and this guide is not legal advice. Testing a
station privately, with your own recordings or music you
have permission to use, is a different matter from
broadcasting to the world — chapter 12 returns to this.

## What this guide covers

- [**What AzuraCast is**](what-azuracast-is.md) —
  the station in a box: what it manages, what it is not,
  and where its edges are
- [**The moving parts**](the-moving-parts.md) —
  Liquidsoap the AutoDJ, Icecast the broadcaster, the
  web app and its helpers, and how a song travels from
  your disk to a listener
- [**Before you install**](before-you-install.md) —
  the machine you need, VPS versus home computer, and
  the port reality: why a radio server wants hundreds
  of open ports
- [**Installing AzuraCast**](installing-azuracast.md) —
  the Docker installer and its utility script, step by
  step, including the four files an installation is
  made of
- [**Your first station**](your-first-station.md) —
  the setup wizard, your administrator account, and
  what a station's settings and auto-assigned ports
  actually mean
- [**Media and playlists**](media-and-playlists.md) —
  getting music in, and the playlist types that decide
  how often each pile of songs gets heard
- [**Scheduling**](scheduling.md) — programmes at set
  times, jingles dropped in once an hour, and the
  public schedule page
- [**Going live**](going-live.md) — the AutoDJ's quiet
  shift, DJ accounts, broadcasting from your desk with
  an encoder program, and the browser-based Web DJ
- [**Listeners, mounts and relays**](listeners-mounts-and-relays.md) —
  the addresses listeners use, the public player page,
  listener statistics, and sending your signal on to
  other servers
- [**The API**](the-api.md) — the now-playing feed
  every player is built on, read in full, and API keys
  for everything else
- [**Backups and updates**](backups-and-updates.md) —
  the backup archive that holds your whole station,
  and the choice between rolling and stable releases
- [**Limits**](limits.md) — one container holding
  everything, the licensing AzuraCast cannot do for
  you, and the rest of the honest list
- [**Troubleshooting**](troubleshooting.md) — the
  silent station, the DJ who cannot connect, listeners
  who cannot hear, and where the logs live
- [**Glossary**](glossary.md) — the terms, collected
  in one place

## What it doesn't cover

Running Icecast or Liquidsoap on their own, without
AzuraCast — they are each a deep subject, and AzuraCast's
whole purpose is that you do not have to. Shoutcast and
RSAS, the two alternative broadcasting frontends AzuraCast
can drive, are named and explained but not configured here;
the guide follows the default Icecast route. Podcasting is
a neighbouring craft (recorded episodes, published on
demand) and is not what a live station does. And the
licensing chapter is a signpost, not a lawyer: which
licences your station needs depends on your country and
your audience, and the licensing bodies there are the ones
to ask.

## How to use this guide

Read chapter 1 for the model and chapter 2 before
installing anything — knowing which part plays the music
and which part serves it makes every later setting legible.
Chapters 3 and 4 get you an installed, empty AzuraCast;
chapter 5 turns it into a station; chapters 6 and 7 fill it
with music and a programme. A station that only ever plays
its AutoDJ can stop there and be complete. Chapters 8 and 9
are for going live and growing an audience. The lab's
dissection notes live in `azuracast-lab/`, and they are the
evidence behind the numbers quoted throughout.

---

## Part of the series

This guide is one of the [For Mere Mortals](https://paulsullivan33-dev.github.io/mere-mortals/) guides: plain-language guides to the tools programmers and makers use.

Related guides:

- [Docker for Mere Mortals](https://paulsullivan33-dev.github.io/docker-for-mere-mortals/)
- [Docker Compose for Mere Mortals](https://paulsullivan33-dev.github.io/docker-compose-for-mere-mortals/)
- [Navidrome for Mere Mortals](https://paulsullivan33-dev.github.io/navidrome-for-mere-mortals/)
- [OBS Studio for Mere Mortals](https://paulsullivan33-dev.github.io/obs-studio-for-mere-mortals/)
- [Backups for Mere Mortals](https://paulsullivan33-dev.github.io/backups-for-mere-mortals/)

## License

The writing in this guide is licensed under [CC BY 4.0](https://github.com/paulsullivan33-dev/azuracast-for-mere-mortals/blob/main/LICENSE). Code examples and lab scripts are licensed under the [MIT License](https://github.com/paulsullivan33-dev/azuracast-for-mere-mortals/blob/main/LICENSE-CODE).
