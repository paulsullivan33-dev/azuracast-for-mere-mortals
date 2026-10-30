# Before You Install

AzuraCast is easy to install and surprisingly easy to
install in the wrong place. This chapter gives you the
machine it needs, the choice between a rented server and a
home computer, and the one fact that decides the question
for most people: a radio station is a **port-hungry**
program, and home networks are where ports go to be
forgotten by routers.

## The machine

AzuraCast runs inside Docker, so the real requirement is "a
computer that runs Docker well", plus enough room for your
music. The project's own figures, from its requirements
page:

- **Minimum:** a 64-bit processor (Intel/AMD x86_64, or
  ARM64 — newer Raspberry Pi models qualify), **2 GB of
  memory**, **20 GB of disk**.
- **Recommended:** 4 processor cores, **4 GB of memory**,
  **40 GB of disk** — enough, the project says, for a hobby
  installation of 5 to 10 stations.

The disk figure is mostly your music library's, not
AzuraCast's. A useful rule: measure your music folder,
double it for backups and working room, and make sure the
machine has that much spare. The project's recommended
hosts are the current Ubuntu and Debian releases; some
cheap hosting built on OpenVZ or LXC containers cannot run
Docker properly and is best avoided — the requirements page
names that incompatibility outright.

Your own computer also needs almost nothing: a web browser.
Everything after installation happens there.

## The port reality

Here is the fact to plan around. A default AzuraCast
installation publishes these ports on its machine
(read directly from the Docker Compose file the installer
delivers):

- **80 and 443** — the web interface, and (via the NGINX
  proxy) a polite way for listeners to reach streams.
- **2022** — SFTP, for uploading music in bulk.
- **8000 to 8496** — the *radio ports*, reserved in blocks
  of ten per station. The first station takes the 8000
  block: **8000** is its Icecast port (where the stream is
  served), **8005** is its DJ port (where a live DJ's
  program connects), and 8006 is also forwarded. The second
  station takes the 8010 block, and so on. The default
  range covers 50 stations.

On a rented VPS, opening ports is a firewall setting and
takes a minute. At home, every one of those ports that must
be reachable from the internet has to be forwarded through
your router to the AzuraCast machine, and some home
connections (notably those behind carrier-grade NAT) cannot
accept incoming connections at all. Listeners can be served
through the 80/443 proxy, which eases the listener side,
but a DJ connecting from outside still needs their DJ port
open, and direct stream links use the radio ports.

So the honest summary:

- **A small VPS is the natural home** for a station with
  public listeners or outside DJs. It is also why the
  project offers one-click installers at several VPS
  providers. A few units of currency a month buys the
  minimum machine comfortably.
- **A home machine is fine** for learning, for a household
  station, and for listeners on your own network — and
  AzuraCast itself does not care where it runs. Just expect
  the router, not AzuraCast, to be the chapter where you
  spend your time.
- **A machine that sleeps is not a server.** The station
  plays only while its host is awake and connected.

## Two more decisions to make now

**A name and, ideally, a domain.** Your installation will
be reached by address: an IP address works, but a domain
name (or a free subdomain from a dynamic-DNS service, for a
home machine) is what you will hand to listeners and what
the built-in Let's Encrypt support needs to give you HTTPS
certificates. Decide it before chapter 4, not during.

**Rolling or stable?** AzuraCast publishes two release
channels and makes you pick during installation: **Rolling
Release** (`latest`), which receives new features and fixes
continuously, and **Stable**, which advances in numbered
releases (0.24.0 was current when this guide was written).
Chapter 11 discusses the choice; for a first installation,
Stable is the calmer ride, and you can switch channels
later.

## The checklist

You are ready for chapter 4 when you have:

- [ ] A machine (rented or home) meeting the minimums,
      running a current Ubuntu or Debian, staying on
      around the clock.
- [ ] The ability to open ports 80, 443 and 2022 on it,
      and the 8000-series radio ports you plan to use.
- [ ] `sudo`, `curl` and `git` available on it (the
      installer checks).
- [ ] An address for it — a domain name if you possibly
      can.
- [ ] Some music, in ordinary files (MP3, Ogg Vorbis,
      FLAC and friends), that you have the right to play
      to your intended audience.
