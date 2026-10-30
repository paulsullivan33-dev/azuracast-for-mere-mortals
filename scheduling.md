# Scheduling

Rotation playlists answer *what may play*. Scheduling
answers *when*. This chapter covers schedule items — the
time windows you attach to playlists — and the handful of
patterns that turn a shuffled library into something that
sounds programmed: a breakfast show, a quiet night, the
news on the hour.

## Schedule items

Any playlist can carry one or more **schedule items**:
a start and end time, the days of the week it applies to,
and optionally a date range. Outside its schedule, a
scheduled playlist is simply not eligible — the AutoDJ
chooses from whatever else is available. Inside it, the
playlist joins (or, for a stream or a special show,
dominates) the rotation.

The times are read in the **station's time zone**, set in
chapter 5. A station broadcasting to its home town from a
server three countries away keeps sane breakfast hours
only if that setting is right; it is the first thing to
check when a programme starts an hour early.

On the playlist's Schedule tab you add items with an
"Add Schedule Item" button and fill in the window. A few
refinements worth knowing:

- **Play once per scheduled period** and **loop** options
  (for playlists that are a single recorded show) let a
  scheduled playlist play its contents once through, or
  repeat them, within its window — rather than feeding
  single songs into a rotation.
- A scheduled playlist can be set to **interrupt** the
  currently playing song when its window opens, for the
  programmes that must start *on* the dot — the news,
  again — rather than at the next song boundary.
- If two scheduled playlists overlap, **priority** breaks
  the tie: the higher-priority playlist plays first.

## The patterns that matter

Most real stations are built from four scheduling patterns:

**Dayparting.** Different rotation playlists for different
parts of the day — brighter music in the morning, the full
library by day, quieter material overnight. Create three
rotation playlists, schedule each to its hours, and leave
them overlapping nowhere. Between them they cover the
clock, which matters: see the warning below.

**The hourly furniture.** Jingles, idents and bulletins
belong in *Once per Hour* or *Once per X Songs* playlists
(chapter 6), not in the schedule. The schedule is for
blocks of time; the once-per types are for insertions.
Mixing the jobs up — a scheduled playlist containing one
30-second ident — produces a station that plays that ident
in a loop for an hour, which has happened to real stations
and is exactly as funny as it sounds, once.

**The recorded show.** A weekly programme, recorded as one
long file or as a folder, in its own playlist scheduled
for its slot, set to play once through. Add a second,
date-limited schedule item for a holiday special; date
ranges keep last Christmas from returning in July.

**The remote programme.** A remote-stream playlist
(chapter 6) joined for its slot: the national news at the
top of the hour, a syndicated show on Saturday morning.
The schedule both joins and leaves it.

## The public schedule

AzuraCast publishes a **public schedule page** for the
station — listeners can see what is planned and when. It
is a small feature with an outsized effect on whether a
station feels real. It is also a promise: a schedule page
showing programmes that never happen, because their
playlists are empty, teaches listeners not to trust it.
Fill a slot before you publish it.

## The warning: silence is scheduled too

The AutoDJ can only play from playlists that are eligible
*right now*. If every playlist on a station is scheduled,
and a gap exists between windows — the overnight playlist
ends at 06:00, the morning one starts at 07:00 — then for
that hour the correct answer to "what plays next?" is
*nothing*, and nothing is what listeners get (AzuraCast
falls back to a default error tone file in place of music,
which is worse than it sounds).

So the standing advice: always keep one **unscheduled**
General Rotation playlist — the safety net — even if it is
just the station's broadest library pile at a low weight.
Programmes pre-empt it; gaps fall back to it; the station
never has to answer a listener with silence.
