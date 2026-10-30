# Your First Station

Installation gave you an empty AzuraCast. This chapter
turns it into a station: you will create the administrator
account, work through the setup wizard, and learn what a
station's core settings mean — above all, the port block
and addresses that every later chapter refers back to.

## The setup wizard

The first time you open your AzuraCast address in a
browser, there are no user accounts at all, so AzuraCast
presents its **setup wizard** instead of a login page. It
asks you to create the first account — the **administrator**
(or *super administrator*), who can see and change
everything in the installation, including other stations
and the server settings under **Administration**.

Choose this password like a master password, because it is
one, and use an email address you actually read: it is also
the account recovery route. Later, from the Administration
pages, you can add more users and **roles** — sets of
permissions, global or per-station — so that, for example,
a presenter can manage one station's music without seeing
server settings. A one-person station can postpone roles
indefinitely; a shared station should not.

## Creating the station

The wizard then walks you through creating your first
**station**. A station is the self-contained unit described
in chapter 1: its own media, playlists, DJs, mount points
and statistics. The decisions that matter at this point:

- **Name.** What listeners will see: on the public page, in
  player apps, and in the "now playing" data. You can
  change it later.
- **Short name (shortcode).** A compact, URL-safe version
  of the name, used inside addresses — your station's
  public page and listen URLs are built from it. Pick
  something short and permanent; renaming it changes every
  link anyone has saved.
- **Time zone.** Schedules (chapter 7) are interpreted in
  the station's time zone. Set it to where the station
  *broadcasts from*, not where the server happens to sit —
  rented servers are often in other countries.
- **Broadcasting defaults.** The wizard and the station
  profile let you set the stream's format and bitrate (a
  typical music station might use MP3 or AAC at 128 kbps;
  speech survives happily at half that). Higher bitrate is
  better sound and more bandwidth per listener — the
  arithmetic is simple and chapter 9 returns to it.

## The station profile page

Each station has a **Profile** page that is its dashboard
and control room in one: what is playing now, whether the
AutoDJ (backend) and the broadcasting service (frontend)
are running, and the station's addresses. It is also where
the **start/stop/restart** controls for both services
live. When anything in the later chapters misbehaves, this
page is the first place to look: two green "running"
indicators forgive a multitude of other sins.

## Your station's ports

When the station was created, AzuraCast assigned it the
first free block of ten radio ports — almost certainly the
8000 block (the assignment code searches upward from 8000,
in blocks of ten, skipping blocks already taken by other
stations). Within the block:

- **8000** — the **frontend port**. Icecast serves the
  stream here; this is the port in the station's direct
  listen address.
- **8005** — the **DJ port**, exactly the frontend port
  plus five. A live DJ's encoder program connects *here*,
  to Liquidsoap, not to Icecast. Chapter 8 lives on this
  number.
- **8004** — Liquidsoap's internal control (telnet) port.
  It is assigned too, but it is *not* forwarded to the
  outside in the default installation, and it should stay
  that way: it is a control channel, not a service for the
  public.

A second station created later takes the 8010 block (DJ on
8015), a third the 8020 block, and so on. Knowing your
station's block by heart will save you real time in
chapter 8 and chapter 13.

## Your station's addresses

With the proxy doing its work (chapter 2), the addresses
listeners use are built on the station's shortcode and the
installation's ordinary web address:

- **Public page:** `/public/<shortcode>` on your AzuraCast
  address — a ready-made player page you can link to or
  embed in your own site.
- **Listen address:** `/listen/<shortcode>/radio.mp3` —
  the stream itself, proxied over the web ports, and the
  address to hand to player apps. The default mount point
  name is `/radio.mp3`; stations can add more mounts
  (chapter 9).
- The profile page also shows the **direct** address on the
  radio port (`http://<server>:8000/radio.mp3`), which
  bypasses the proxy — useful for troubleshooting and for
  relay software, less suitable as the public link.

## A check before you go on

From the station profile you should now be able to point
at: both services running, your station's three port
numbers, and its public-page address. The station is
silent, of course — it has no music and nothing scheduled.
That is chapter 6's happy problem.
