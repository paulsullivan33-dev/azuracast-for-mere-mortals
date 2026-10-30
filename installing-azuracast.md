# Installing AzuraCast

By the end of this chapter AzuraCast will be installed and
running on your machine, and you will know exactly which
files the installation consists of — because AzuraCast's
installer is unusually inspectable, and this chapter was
written by reading it. The installation itself is one
script; the chapter's real payload is what that script
does, so that nothing about your station is magic later.

## The shape of the install

AzuraCast's supported installation method is **Docker**.
There is no bare-metal installer to run on the host: the
host needs Docker and Docker Compose, and everything else
arrives as container images. If the words Docker and
Compose are new, the sibling guides *Docker for Mere
Mortals* and *Docker Compose for Mere Mortals* cover them;
the one-sentence version is that Docker runs programs in
sealed containers, and Compose is the file format that
describes a set of containers to run together.

The installation lives in one directory — **`/var/azuracast`**
is the recommended home — and when it is finished it
consists of four files:

- **`docker.sh`** — the *Docker Utility Script*. Nearly
  everything you will ever do from the command line —
  install, update, back up, restore, uninstall — is a
  subcommand of this one script.
- **`docker-compose.yml`** — the Compose file describing
  the containers. It is delivered by the installer and
  refreshed by updates; you are advised *not* to edit it
  directly (there is an override file for that — see
  "Customising" below).
- **`.env`** — settings for Docker Compose itself: the
  project name, and the host ports for web (80), HTTPS
  (443) and SFTP (2022).
- **`azuracast.env`** — settings sent to AzuraCast itself:
  its database and cache details, the radio-port range to
  assign from, and more. Updates never replace this file;
  it is yours. One warning from the documentation deserves
  repeating verbatim in spirit: once the database exists,
  changing the database password in this file will break
  the installation, because the database kept the old one.

## Step 1: Get the utility script

On the machine, as a user with `sudo`:

```bash
mkdir -p /var/azuracast
cd /var/azuracast
curl -L https://raw.githubusercontent.com/AzuraCast/AzuraCast/main/docker.sh > docker.sh
chmod a+x docker.sh
```

(The one-click installers at VPS providers, and prebuilt
images some hosts offer, arrive with this already done.)

## Step 2: Run the installer

```bash
./docker.sh install
```

Reading the script, here is what that command actually
does, in order:

1. **Checks the machine.** Linux or macOS; a 64-bit Intel
   or ARM processor; `sudo`, `curl` and `git` present. It
   also gently warns if you are not installing into
   `/var/azuracast`.
2. **Installs Docker and Docker Compose if they are
   missing** (it asks first). If it finds Podman instead,
   it can use that, with podman-compose.
3. **Asks your release channel** — Rolling Release or
   Stable (chapter 3's decision), recorded as the image
   tag in `.env`.
4. **Runs the installer container**, which writes your
   `docker-compose.yml`, your environment files, creates
   the database and prepares the storage volumes.
5. **Downloads the images and starts everything**
   (`docker compose pull`, then `up -d`).

When it finishes, two containers are running: `azuracast`
(the whole station) and `azuracast_updater` (the small
helper that applies image updates).

## Step 3: Confirm it is alive

```bash
docker ps
```

You should see both containers listed as running. Then
open `http://<your machine's address>/` in a browser: the
AzuraCast setup wizard should greet you. Chapter 5 takes
it from there. If the page does not load, chapter 13's
first entries are written for exactly this moment — but
the usual suspects are the machine's firewall (ports 80/443
not yet open) rather than AzuraCast itself.

## Customising, the sanctioned way

Sooner or later you will want the web interface on
different ports, extra storage mounted in, or the radio
range widened past 50 stations. The rules, from the
project's Docker documentation:

- Do not edit `docker.sh` or `docker-compose.yml`.
- Put Compose changes in **`docker-compose.override.yml`**
  in the same directory; Compose applies it on top, and
  updates never touch it.
- Change the web/SFTP ports with the utility script's own
  helper: `./docker.sh change-ports`.
- Edit `azuracast.env` for application settings (like
  `AUTO_ASSIGN_PORT_MIN`/`MAX` if you widen the radio
  range) — and remember the database-password warning
  above.

For HTTPS with a real domain, the script can also fetch
Let's Encrypt certificates: `./docker.sh setup-letsencrypt`.

## What the lab verified about all this

This guide's lab could not run the installer (no Docker on
the lab machine), so the steps above are not a transcript.
They are the installer's own behaviour, read from the
project's `docker.sh` (953 lines), its
`docker-compose.sample.yml` and its sample environment
files at version 0.24.0, cross-checked against the
project's installation and Docker administration pages.
Where a reader's run will differ — prompts answered,
provider panels clicked — the text says "the script asks"
rather than inventing dialogue. `criteria.md` keeps the
full ledger.
