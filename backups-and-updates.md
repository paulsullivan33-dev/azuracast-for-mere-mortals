# Backups and Updates

A radio station accretes value quietly: the library, the
tag fixes, the playlists, the schedules, the listener
history. This chapter covers keeping it — the backup
archive that holds the whole installation, how restores
behave, and the release-channel choice that decides how
your AzuraCast changes over time.

## What a backup contains

The utility script's backup command:

```bash
cd /var/azuracast
./docker.sh backup [/path/to/backup.tar.gz]
```

runs the backup inside the AzuraCast container (the
archive is written to the installation's `backups`
volume) and then moves it out to the path you name on the
host, `backup.tar.gz` by default. Per the project's
documentation, the archive contains **the media,
statistics and metrics of every station, plus a full copy
of the AzuraCast database** — which is to say, everything
that makes your installation *yours* rather than a fresh
install. Backups can also be run, scheduled and downloaded
from the Administration pages in the web interface, which
is where most people end up managing them.

Two disciplines turn "backups exist" into "backups work":

- **The archive must leave the machine.** A backup stored
  on the server it protects is a copy of your station's
  history stored in the same fire, theft and
  disk-failure as the original. Download archives to
  somewhere else — the general guide *Backups for Mere
  Mortals* is the series' treatment of doing this
  properly.
- **The archive is not the configuration files.** Your
  `/var/azuracast` folder — `.env`, `azuracast.env`, any
  `docker-compose.override.yml` — is tiny and worth
  copying alongside the archives. A restore rebuilds the
  station's *contents*; those files are what make the
  rebuilt installation *yours* at the network level.

## Restoring, and what it costs

```bash
./docker.sh restore /path/to/backup.tar.gz
```

The script warns, and the warning is worth reading twice:
**restoring removes the existing installation's data and
replaces it with the backup's.** A restore is not an
import or a merge; it is a rewind. The sensible uses are
the two sad ones — the server died, the data is corrupt —
and the one cheerful one, moving the whole station to a
new machine: install AzuraCast there, restore the archive,
reapply your environment files, and the station continues
with its library, playlists and history intact.

Because a restore is destructive, try it before you need
it: restore last month's archive into a spare machine or a
throwaway virtual machine once, and learn in peacetime
that the archive is good. Chapter 4's Docker is all a
test restore needs.

## Updates and the two channels

Chapter 4 left you with a channel choice. Here is what it
means in practice:

- **Rolling Release** (the `latest` image tag): AzuraCast
  as it is finished, feature by feature. The project's
  changelog literally begins with a "Rolling Release
  Changes" section listing what has merged since the last
  numbered release. You get grouped playlists the week
  they are finished; you also get the occasional
  Monday-morning surprise, and you are the project's early
  warning system.
- **Stable** (the `stable` tag, or a pinned version like
  `0.24.0`): numbered releases, taken together after
  testing. Features arrive weeks or months later; mornings
  are calmer.

Neither is the "wrong" answer. A public station with an
audience tends to choose Stable; a home station that
enjoys new toys chooses Rolling. You can move between
channels from the installer, and pinning a specific
version is the escape hatch when one release misbehaves
for you.

## The update procedure

```bash
cd /var/azuracast
./docker.sh update-self
./docker.sh update
```

The order matters and the documentation insists on it:
`update-self` first, so the script doing the updating is
current. Then `update`, which — read from the script —
checks the Docker version, re-runs the installer in update
mode, offers to replace your `docker-compose.yml` with the
new sample (accept, unless you edited it against advice;
your old one is kept as `docker-compose.backup.yml`, and
your `docker-compose.override.yml` is never touched),
pulls the new images, applies database migrations, restarts
the containers, and offers to prune the old images to
reclaim disk.

Expect a brief interruption to listeners while the
container restarts — schedule updates for your quietest
hour, and take a backup first, because the correct order
of operations for any update, anywhere, forever, is:
**backup, then update, then check the station is playing.**

A word on the `azuracast_updater` container: it watches
for image updates and can apply them itself, which is why
some installations update without anyone running anything.
Know that it exists and decide deliberately whether you
want updates to arrive on the project's schedule or on
yours; the manual procedure above always remains
available, and for a station with listeners, "I choose when"
is a defensible default.
