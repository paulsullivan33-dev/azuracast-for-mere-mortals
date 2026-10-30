# Limits

Every tool has edges. This chapter collects AzuraCast's,
plainly, so that nothing in the earlier chapters reads as
a promise it cannot keep.

## It only runs the Docker way

AzuraCast's supported life is the Docker installation of
chapter 4. There is no supported bare-metal install on a
modern system, no Windows service, no "just run the PHP"
route that the project will help you with. (An older
Ansible-based installer exists in the project's history
and is explicitly unsupported.) If Docker is a hard
objection — a host that forbids it, an organisation that
bans it — AzuraCast is the wrong tool, and chapter 2's
components (Liquidsoap and Icecast, run directly) are the
right, harder road.

## One container holds everything

The current design puts the application, its database, its
cache and the radio programs in a single container. That
is a deliberate simplicity: one thing to install, back up
and update. The costs are the mirror image of the benefits:
you cannot scale the pieces separately, restart the
database without restarting the radio, or move the
database to a managed service without leaving the
supported path. AzuraCast answers growth mostly with
relays (chapter 9) and a bigger single machine — which
covers a great many real stations, but not all of them.

## The configuration is generated, not yours

AzuraCast writes Liquidsoap's and Icecast's configuration
from its database. Hand-editing those files is overwritten
at the next change, and some customisation that the
underlying programs support is simply not exposed in the
web interface. Advanced users can step outside the fence —
custom Liquidsoap code in advanced playlists and custom
frontend configuration fields exist — but they do so
knowingly, and support forums will gently return them to
the fence.

## Ports are the price of admission

A station is a block of ten ports; the default firewall
posture opens five hundred of them; home routers and
restrictive hosts make this the project's recurring
practical headache (chapter 3). The web proxy covers
listeners, but DJ connections and direct stream addresses
still live out on the radio ports. Installations on hosts
where you cannot open ports will be diminished in ways no
setting fixes.

## It brings neither music nor listeners — nor licences

AzuraCast cannot fill your library, find your audience,
or — the one with legal weight — **license your
broadcasts**. Playing recorded music to the public
generally requires licences from the bodies that collect
royalties for songwriters and for recordings, and the
details differ by country and by how your station is
heard. The cost is real and belongs in a public station's
budget from day one. Private testing, your own recordings,
and music you have explicit permission to use are
different cases — but "the software let me" is not a
licence, and "nobody has noticed yet" is not a strategy.
This guide is not legal advice; the licensing bodies in
your country are the authority.

## Smaller print

- **Listener numbers are approximate.** Icecast counts
  connections, not ears (chapter 9), and reports inherit
  that softness.
- **The AutoDJ is not a programme director.** It follows
  your rules exactly, including the foolish ones; it will
  happily play the same artist twice through a weight
  accident you made. It prevents exact song repeats; taste
  remains your job.
- **Rolling means rolling.** On the Rolling Release
  channel, behaviour can change under you between updates
  — the price of the newest features (chapter 11).
- **It is one project.** AzuraCast is free software
  maintained by a small team. It is mature, widely used
  and actively released (0.24.0 was October 2026), but
  there is no support contract behind it — the community
  and the documentation are the support desk.
