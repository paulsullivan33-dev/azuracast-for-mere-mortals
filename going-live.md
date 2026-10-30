# Going Live

Everything so far was the station playing itself. This
chapter is about a human being taking over: how the handover
works, the DJ accounts that control it, and the two ways to
broadcast from your desk — an encoder program, or nothing
but a web browser.

## The handover

Recall the shape from chapter 2: Liquidsoap, the AutoDJ,
is always assembling the stream. It also holds a listening
socket open — the **harbor** — on your station's **DJ port**
(the frontend port plus five: 8005 for the first station).
When a DJ's program connects there and authenticates,
Liquidsoap fades the automatic programme out and puts the
live input on air; when the DJ disconnects, it fades the
AutoDJ back in. Listeners, connected to Icecast throughout,
hear a continuous stream with a person suddenly in it.
Nothing restarts; nobody has to press anything on the
server. The AutoDJ is the station's autopilot, and a live
DJ is a pilot taking the controls of a plane that keeps
flying either way.

Two consequences follow. First, a station with live DJs
still needs its AutoDJ library in good shape — it is what
plays before, after, and during every technical mishap.
Second, live broadcasting can be *scheduled per DJ*: an
account can be allowed to connect only inside its own time
windows.

## DJ accounts

Live access is per person. On the station's
**Streamers/DJ Accounts** page you enable streamers for the
station and add an account for each DJ:

- a **username** and **password** — these are the encoder
  credentials, separate from any web login;
- a **display name**, shown to listeners as the current
  presenter;
- whether the account is **active**, and optionally its
  **schedule** — the times it may connect.

The same page shows the station's **Connection
Information**: the server address and the DJ port. That
page is the whole contract between the station and its
DJs; everything an encoder needs comes from it.

Give every DJ their own account, even — especially — if
you trust them all. Shared credentials cannot be revoked
one person at a time, and the statistics about who was
live, and when, are only as good as the accounts are
distinct.

## Broadcasting from your desk: an encoder

An **encoder** is a program that takes your microphone and
music and sends them to the station as a stream. The
project's streaming-software documentation covers several;
the two a new DJ is most likely to meet:

- **BUTT** ("Broadcast Using This Tool") — free, simple,
  and the classic choice. It does one job: connect, send
  audio, show a big reassuring button. AzuraCast's
  documentation gives its settings in detail: create a
  server entry of type **Icecast**, with the address and
  port from the Connection Information page, your DJ
  username, and your DJ password. (AzuraCast accepts
  Shoutcast-style connections too — Liquidsoap is what
  actually receives you, so the mode matters little — but
  the documentation recommends Icecast mode as the more
  modern of the two.)
- **Mixxx** — free DJ software with decks, mixing and a
  microphone input, which can also broadcast. If you are
  actually *DJing* rather than presenting, this is the
  tool; its live-broadcasting preferences take the same
  connection information (the documentation suggests
  "Icecast 2" mode, Ogg Vorbis format — Liquidsoap
  transcodes it to your station's output format for you).

Commercial desks like RadioBOSS and SAM Broadcaster work
the same way, with the same four facts: **server, DJ port,
username, password**. Whoever supports your station's DJs
will repeat those four facts often. Write them on the
station's internal notes page, not in public.

Connection refused, or authentication failed, at this
point is almost always one of: the frontend port used
instead of the DJ port (the classic), the DJ port blocked
by the server's firewall or a home router, or the account
inactive or outside its schedule. Chapter 13 lists them in
order.

## Broadcasting from your desk: the Web DJ

AzuraCast also includes a **Web DJ**: a page in the
station's web interface where a signed-in DJ account can
broadcast live from the browser — microphone plus a small
two-deck player for music files — with no encoder to
install or configure. For occasional presenters, guests,
and anyone for whom "install this program and type these
five settings correctly" is a genuine barrier, it removes
the entire setup problem. Its limits are a browser's
limits: keep the tab open and the laptop awake, and expect
less polish than a real desk. Many stations run both:
encoders for the regulars, the Web DJ for everyone else.

## While you are live

The station's profile and public pages show the live
state — the API's `live` object (chapter 10) carries
whether a DJ is live, their display name, and when the
broadcast started — and AzuraCast can record live
broadcasts, if you enable recording for the station, so a
show can be re-played later from the recordings page.
When you disconnect, give the fade a moment: the AutoDJ
resuming underneath you is the sound of the system working
as designed, not of anything breaking.
