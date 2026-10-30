# Listeners, Mounts and Relays

A station exists at the moment somebody hears it. This
chapter covers the addresses listeners use, why one station
can have several streams at once, the public face AzuraCast
gives every station, how listeners are counted — and how to
pass your signal on to other servers when one machine is
no longer the whole story.

## Mount points: one station, several streams

Icecast serves each distinct stream at a **mount point** —
a path like `/radio.mp3`. A station starts with one mount,
in the format and bitrate chosen in its profile. It can
have more: a high-quality mount for home listeners and a
low-bitrate mount for phones on poor connections, or a
mount carrying a different programme. Each mount is a
setting on the station's **Mount Points** page, with its
own format, bitrate and name, and the AutoDJ feeds them
all from the same programme.

Bitrate is the number to think with. A listener on a
128 kbps mount consumes 128 kilobits per second for as
long as they listen — about 58 megabytes per hour. A
hundred simultaneous listeners on that mount need a steady
12.8 Mbps of your server's outbound bandwidth. That
arithmetic, more than any other, is what sizes a station's
hosting.

## The two ways in

Listeners can reach a mount two ways (chapter 2's proxy is
the reason both exist):

- **Directly**, on the station's Icecast port:
  `http://<server>:8000/radio.mp3`. Simple, and what relay
  software and some player apps prefer — but many
  workplace, school and hotel networks block ports in the
  8000s outright, and plain HTTP on an odd port cannot
  carry an HTTPS address.
- **Through the proxy**, on the ordinary web ports, at
  `/listen/<shortcode>/radio.mp3` under your installation's
  normal HTTPS address. This is the address to publish: it
  works everywhere the web page works, and it is what the
  public page's player uses.

## The public page

Every station gets a **public page** at
`/public/<shortcode>`: a working player, the current and
recent songs, the station's schedule (chapter 7), and a
request form if requests are enabled. You can use it as
the station's whole web presence, embed its player into
your own site, or ignore it and build your own front end
on the API (chapter 10). The station's branding — name,
description, and the installation's custom CSS if you go
that far — is what makes it look like *your* station
rather than a software demo.

## Counting listeners, honestly

AzuraCast reports listener numbers per station and per
mount, now and over time, and keeps **reports**: listeners
by hour and day, each song's effect on the audience (the
song that makes people leave is a real and findable
thing), and an export formatted for SoundExchange, the US
body that collects performance royalties for recordings
played by webcasters.

Two honest notes belong with any statistics chapter:

- **These are your listeners' data.** AzuraCast records
  connections, including listeners' IP addresses, and uses
  a geolocation database (the GeoLite data among the
  installation's volumes) to report roughly where listeners
  are. That is normal for a broadcaster and genuinely
  useful; it is also a small database of strangers'
  behaviour that you are now responsible for storing
  sensibly and not sharing carelessly. Your country's
  privacy law may have views about retention; a hobby
  station can satisfy most of them by not keeping exports
  forever and not publishing listener-level detail.
- **A connected listener is not a person listening.** A
  player left running in an empty room counts. Treat the
  numbers as weather, not as census.

## Relays: sending the signal onward

A **remote relay** is another Icecast or Shoutcast server
that also carries your station — a second machine closer
to a far-away audience, a directory's own server, a
friend's relay. AzuraCast knows two kinds, in its
documentation's own terms:

- **Statistics Only.** The remote server relays your
  broadcast by its own arrangement (typically it is
  configured to pull your stream), and AzuraCast merely
  *reads its listener statistics* and adds them to yours.
- **Full Relay.** AzuraCast itself pushes the station's
  signal to the remote server, and likewise gathers its
  statistics.

Separately, the project maintains **AzuraRelay**, a small
companion program: install it on a spare VPS, point it at
your AzuraCast, and it automatically relays all your
stations and reports listener numbers back. For a station
that has outgrown one machine's bandwidth, a chain of
AzuraRelay boxes is the intended growth path — the
broadcast is produced once, centrally, and the cheap
serving copies multiply.

Relay statistics are folded into the station's own
numbers, which is the point: the audience is one audience,
wherever it connected.
