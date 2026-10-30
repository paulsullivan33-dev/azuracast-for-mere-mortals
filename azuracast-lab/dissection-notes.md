# Lab notes: dissecting AzuraCast from its own artefacts

AzuraCast's only supported install route is Docker Compose, and
this lab has no Docker daemon and cannot run containers, so
nothing in this folder is a transcript of a running AzuraCast.
Instead, every concrete claim in the guide was taken from the
project's own files or its official documentation, as itemised
below. The guide's `criteria.md` repeats the summary of this
ledger.

## The source tree

- Repository: https://github.com/AzuraCast/AzuraCast
- Shallow clone taken 2026-10-30; HEAD commit
  `a683c9dbb09ddab3fd6eec6793e691dc0575ff57` (2026-10-26).
- Latest stable release in that tree: **0.24.0** (Oct 25, 2026),
  per `CHANGELOG.md` and `backend/src/Version.php`
  (`STABLE_VERSION = '0.24.0'`). The clone was deleted after
  dissection; these notes are what remains.

## docker-compose.sample.yml (the file the installer delivers)

- Two services only:
  - `web` (container name `azuracast`), image
    `ghcr.io/azuracast/azuracast:${AZURACAST_VERSION:-latest}` —
    one all-in-one container holding the web app, MariaDB,
    Redis, Liquidsoap and Icecast (its volumes include
    `db_data:/var/lib/mysql` and
    `station_data:/var/azuracast/stations`).
  - `updater` (container name `azuracast_updater`), image
    `ghcr.io/azuracast/updater:latest`, mounted on the host's
    Docker socket; applies image updates (a Watchtower-style
    helper — the web service carries the label
    `com.centurylinklabs.watchtower.scope=azuracast`).
- Host ports published by the sample file:
  - HTTP `${AZURACAST_HTTP_PORT:-80}`, HTTPS
    `${AZURACAST_HTTPS_PORT:-443}`, SFTP
    `${AZURACAST_SFTP_PORT:-2022}` (SFTPGo; the
    `sftpgo_data` volume) for bulk media upload.
  - Radio ports **8000–8496**, in a repeating pattern per
    station block of ten: the base port, base+5 and base+6
    (8000, 8005, 8006, 8010, 8015, 8016, …). The docs'
    "Expanding the Station Port Range" section explains the
    default range covers the first 50 stations.
- Named volumes: `station_data`, `backups`, `db_data`,
  `www_uploads`, `shoutcast2_install`, `stereo_tool_install`,
  `rsas_install`, `geolite_install` (GeoLite listener
  geolocation data), `sftpgo_data`, `acme` (Let's Encrypt
  certificates).

## How the per-station ports are assigned (backend source)

`backend/src/Radio/Configuration.php`, `assignRadioPorts()`:

- The station's **frontend (Icecast) port** is the base port of
  the first free 10-port block, searched from
  `AUTO_ASSIGN_PORT_MIN` (default 8000) to
  `AUTO_ASSIGN_PORT_MAX` (default 8499).
- **DJ port = base + 5** (`dj_port = $basePort + 5`). Live DJs
  connect to Liquidsoap's "harbor" input here
  (`input.harbor(...)` is generated in
  `backend/src/Radio/Backend/Liquidsoap/ConfigWriter.php`,
  with an `azuracast.dj_auth` authentication function).
- **Liquidsoap telnet port = base + 4** (internal control; it
  is *not* in the forwarded list in the sample compose file).
- `enumerateDefaultPorts()` forwards base, base+5 and base+6.

## azuracast.sample.env (becomes `azuracast.env` on install)

- `APPLICATION_ENV=production`, `COMPOSER_PLUGIN_MODE=false`.
- `AUTO_ASSIGN_PORT_MIN=8000`, `AUTO_ASSIGN_PORT_MAX=8499`.
- Database defaults documented in comments: MariaDB on
  localhost:3306, user `azuracast`, database `azuracast`;
  the file warns "Once the database has been installed, DO
  NOT CHANGE these values!"
- Redis defaults: localhost:6379, database 1.
- The host-side `.env` (from `sample.env`) is separate and
  only affects Docker Compose itself: `COMPOSE_PROJECT_NAME`,
  `AZURACAST_HTTP_PORT=80`, `AZURACAST_HTTPS_PORT=443`,
  `AZURACAST_SFTP_PORT=2022`, `AZURACAST_PUID/PGID=1000`.

## docker.sh (the Docker Utility Script, 953 lines)

Functions read in full or in part: `install`, `update`,
`update-self`, `backup`, `restore`, `uninstall`, `rollback`,
`setup-letsencrypt` / `letsencrypt-create`, `change-ports`,
`setup-ports`, `setup-release`, `check-install-requirements`.

- `check-install-requirements`: Linux or macOS; x86_64 or
  aarch64 only; wants `sudo`, `curl`, `git`; recommends the
  install live in `/var/azuracast`.
- `install`: installs Docker and Docker Compose v2 if missing
  (or Podman + podman-compose if Podman is detected), runs
  `setup-release` (choose a release channel), runs the
  installer container (`dc run --rm web -- azuracast_install`),
  which produces `docker-compose.new.yml` that is moved into
  place as `docker-compose.yml`; then `dc pull` and
  `dc up -d`.
- Release channels (`setup-release`): the image tag
  `AZURACAST_VERSION` is `latest` (**Rolling Release**),
  `stable` (**Stable**), or a pinned version number.
- `update`: updates docker.sh itself first if it changed,
  re-runs the installer with `--update`, offers to replace
  `docker-compose.yml` with the new sample (keeping the old
  one as `docker-compose.backup.yml`), pulls, restarts, and
  offers `docker system prune`.
- `backup [path]`: runs `azuracast:backup` inside the web
  container writing into the `backups` volume, then moves the
  archive out to the host path (default `./backup.tar.gz`).
- `restore`: warns that it removes existing installation data
  and replaces it with the backup's.

## The API, from the shipped OpenAPI specification

- The repo ships the generated spec at `web/static/openapi.yml`
  (OpenAPI 3.0.0, spec version 0.24.0, 11,432 lines); a running
  installation also serves it at `GET /api/openapi.yml`
  (`backend/config/routes/api_public.php`).
- Public, unauthenticated endpoints include `GET /api/status`,
  `GET /api/time`, `GET /api/stations`,
  `GET /api/nowplaying` and `GET /api/nowplaying/{station_id}`
  (both marked `security: []` in the spec).
- Everything else uses an API key sent in the **`X-API-Key`
  header** (security scheme `ApiKey`, `type: apiKey`,
  `in: header`). Per the docs, a user's API keys carry the
  same permissions as that user.
- `Api_NowPlaying` schema requires: `station`, `listeners`,
  `live`, `now_playing`, `playing_next`, `song_history`,
  `is_online`, `cache`. `Api_NowPlaying_Station` includes
  `listen_url`, `public_player_url`, `playlist_pls_url`,
  `playlist_m3u_url`, `mounts`, `remotes`. `Api_NowPlaying_Live`
  has `is_live`, `streamer_name`, `broadcast_start`, `art`.
  The worked example in the guide's API chapter is assembled
  from these schema fields and labelled as such.
- The spec's example server is the public demo,
  `https://demo.azuracast.com/api`.

## The streaming stack (docs "About" page + repo)

Liquidsoap is the AutoDJ (backend); Icecast-KH is the default
broadcasting frontend; Shoutcast 2 DNAS is optional on x86_64
(non-free, installed from the admin panel after install);
Rocket Streaming Audio Server (RSAS) is a further closed-source
frontend option (the `rsas_install` volume, and the admin API
has `/admin/rsas`). Supporting services inside the container:
NGINX (web + the radio proxy), MariaDB, PHP, Redis, Centrifugo
("Now Playing" feeds). An nginx proxy lets listeners reach
streams over ports 80/443 instead of the 8000-series ports.

## Official documentation pages read (azuracast.com, 2026-10-30)

- About / feature list: https://www.azuracast.com/docs/
- Requirements: https://www.azuracast.com/docs/getting-started/requirements/
  — minimum 64-bit x86_64 or ARM64 CPU, 2 GB RAM, 20 GB disk;
  recommended 4 cores, 4 GB RAM, 40 GB disk ("5 to 10
  stations"); OpenVZ/LXC hosts can be incompatible with
  Docker.
- Installation overview: https://www.azuracast.com/docs/getting-started/installation/
  — one-click installers at Linode, Vultr, DigitalOcean;
  Docker recommended; WSL2 and Synology guides exist.
- Docker administration: https://www.azuracast.com/docs/administration/docker/
  — the four install files; `docker.sh` command reference;
  customise via `docker-compose.override.yml`, never by
  editing `docker-compose.yml` or `docker.sh`; non-standard
  ports via `./docker.sh change-ports`; expanding the station
  port range.
- Station management: https://www.azuracast.com/docs/user-guide/station-management/
  — a station is its own entity (media, playlists, DJs, mounts,
  relays, statistics); backend (AutoDJ) vs frontend
  (broadcasting) explained.
- Remote relays: https://www.azuracast.com/docs/user-guide/remote-relays/
  — two types, "Statistics Only" and "Full Relay"; the
  separate AzuraRelay project for pull relays.
- Streaming software: https://www.azuracast.com/docs/user-guide/streaming-software/
  — BUTT, Mixxx and others; BUTT should use Icecast mode;
  connection details come from the station's
  "Streamers/DJ Accounts" page.

Playlist behaviour (types: General Rotation with weights,
Once per X Songs, Once per X Minutes, Once per Hour, Advanced;
playback order: shuffled, random, sequential; scheduling via
schedule items) is described in the project's documentation
and matches the playlist code paths in
`backend/src/Radio/Backend/Liquidsoap/ConfigWriter.php`
(standard playlists use weighted `random()`, scheduled ones
time-based `switch()`, remote-stream playlists `input.http()`).
Grouped/nested playlists ("clockwheels") and request-queue
playlists are new in 0.24.0 per the changelog.
