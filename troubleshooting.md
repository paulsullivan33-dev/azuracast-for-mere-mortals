# Troubleshooting

The failures below are the ones AzuraCast stations
actually meet, in roughly the order a new station meets
them. Each entry names the check that settles it. A
general principle first: AzuraCast keeps logs for every
service, viewable in the web interface's log viewer under
Administration, and most mysteries in this chapter are
answered there faster than anywhere else. From the command
line, `./docker.sh cli` runs AzuraCast's own tools inside
the container, and `docker compose logs` shows the
container talking to itself.

## The web page does not load at all

The containers are the first question: on the host,
`docker ps` should list `azuracast` and
`azuracast_updater` as running. If they are absent,
`cd /var/azuracast && docker compose up -d` starts them,
and `docker compose logs` shows why they stopped. If they
are running but the page does not answer from your
browser, the suspects are the machine's firewall and, at
home, the router: ports 80 and 443 must be open, and
forwarded if there is a router in the way. Try the page
from the machine itself (or another device on the same
network) to separate "AzuraCast is down" from "the world
cannot reach it".

## The station is silent

Work outward from the station's Profile page:

1. **Are both services running?** The profile shows the
   AutoDJ (backend) and broadcasting (frontend) status,
   with restart controls. A stopped AutoDJ is the most
   common silence of all.
2. **Is there anything to play?** The AutoDJ needs at
   least one enabled playlist with songs in it, eligible
   *now*. Music that is uploaded but not in a playlist
   does not exist, as far as the radio is concerned
   (chapter 6); music in a playlist whose schedule has
   ended is equally absent (chapter 7's warning about
   gaps, and its safety-net advice).
3. **Does "now playing" change?** If the profile's now
   playing display cycles through songs, the AutoDJ is
   working and the problem is downstream — see the next
   entry.

## It plays, but listeners cannot hear it

The stream is playing on the profile page but the public
listen address fails for listeners. Separate the two
routes of chapter 9: try the **direct** address
(`http://<server>:8000/radio.mp3`) and the **proxied** one
(`/listen/<shortcode>/radio.mp3`) from outside the
network. Direct works but proxied does not: the web side
or the station's proxy settings need attention, and the
station profile's address settings are the place. Neither
works from outside but both work on the local network:
firewall or router port forwarding — the radio port (or
80/443 for the proxy) is not open to the world. Proxied
works but direct does not: common, often harmless (many
networks block 8000-series ports on the *listener's*
side), and the reason the proxy is the address to publish.

## A DJ cannot connect

In the order DJs actually cause them:

1. **Wrong port.** The encoder must use the **DJ port** —
   the station's Icecast port plus five (8005 for the
   first station) — not the port in the listen address.
   This is the classic, ahead of everything else combined.
2. **The port is not reachable.** The DJ port must be open
   in the server's firewall (and forwarded, on a home
   install). It is not covered by opening 80/443.
3. **The account.** The streamer account must exist, be
   *active*, and — if it carries a schedule — be inside
   its allowed window. Passwords for encoders are the DJ
   account's password, not the DJ's web password.
4. **The encoder's mode.** Use Icecast mode, as the
   project's streaming-software page recommends; a
   Shoutcast-mode encoder pointed at the wrong port
   variant produces very confusing errors.

## Songs repeat too soon, or never play

The scheduler draws from eligible playlists by weight.
Repeats usually mean the *effective* library is much
smaller than the library on disk: few songs in rotation
playlists, many songs parked in scheduled or
once-per-hour playlists, or heavy weights on a small
playlist. The fix is arithmetic, not settings archaeology:
count what is actually in general rotation. A song that
never plays is usually in no enabled playlist, in a
playlist whose schedule never arrives, or — check the
media manager — a file that failed to be analysed on
upload and is quietly marked unplayable.

## The update went wrong

Restore calm in this order: check the station is playing
(the update restarts containers; give it a minute); read
`docker compose logs` for the container that will not
start; confirm your `docker-compose.yml` was accepted
for replacement during the update and your
`docker-compose.override.yml` is still valid YAML — an
override written against an old version is the usual
suspect. If the installation is truly broken, the
project's `docker.sh` offers a **rollback** to the
previous version, and chapter 11's backup — you did take
one before updating — is the floor under everything.

## Listeners vanished after moving or renaming

Addresses are forever: changing a station's shortcode, or
the installation's domain, breaks every saved link,
embedded player and directory listing that pointed at the
old address. The station is fine; the signposts moved.
Re-publish the new addresses everywhere you published the
old ones, and treat renames as launches, not edits.
