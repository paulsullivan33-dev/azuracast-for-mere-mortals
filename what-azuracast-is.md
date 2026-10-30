# What AzuraCast Is

AzuraCast is a **web radio management suite**: one program
that installs and runs everything a small internet radio
station needs, and presents it all through a web page. By
the end of this chapter you will be able to say what it
manages for you, what it deliberately leaves to other
software, and whether it is the right shape of tool for the
station you have in mind.

## The job it does

A working web radio station needs four things at once:

- somewhere to keep the music, with its titles and artists;
- something that decides what plays next, all day and all
  night, including at 3 a.m. when nobody is awake;
- something that serves the resulting audio to listeners,
  possibly hundreds of them at once;
- a way for a live presenter to take over, talk, play their
  own music, and hand back.

Traditionally each of those is a separate program with its
own configuration files. AzuraCast bundles them. You install
one thing; you get a web page where you upload music, build
playlists, create DJ accounts, and watch listener numbers.
Underneath, two well-known radio programs do the actual
radio work — **Liquidsoap** decides what plays (AzuraCast
calls it the **AutoDJ**, or the *backend*), and **Icecast**
serves the audio to listeners (the *frontend*). AzuraCast
writes both programs' configuration for you, keeps them
running, and shows you their status. Chapter 2 takes the
machine apart properly.

## A station, and many stations

Inside AzuraCast, the unit of everything is a **station**.
A station has its own music library, playlists, DJs, stream
addresses and statistics. One AzuraCast installation can run
many stations at once — a music station and a talk station
side by side, say — limited by the size of your server and,
as chapter 3 explains, by how many network ports you can
open. Most people run exactly one, and that is a perfectly
respectable size.

## What it is not

- **It is not a streaming service.** AzuraCast brings no
  music and no listeners. The library is yours to fill and
  the audience is yours to find.
- **It is not a licence to broadcast.** Playing recorded
  music to the public requires licences in most countries,
  and no software includes them. This is stated here, in
  chapter 1, because it is a real cost of running a public
  station and you should know before you build one. A
  private test stream for your own household, or a station
  playing your own recordings, is a different situation;
  chapter 12 says more, and the licensing bodies in your
  country are the authority, not this guide.
- **It is not a podcast host.** A station is a live,
  continuous stream: everyone who tunes in hears the same
  moment. Recorded shows can be *played* on a station, but
  publishing episodes for on-demand listening is a different
  craft with different tools.
- **It is not a desktop program.** AzuraCast is server
  software. It lives on a machine that stays on, and you
  visit it in a web browser — which is exactly what a radio
  station needs, since the station must keep playing while
  your laptop is asleep.

## Who it suits

AzuraCast suits community stations, hobby stations, school
and workplace stations, and anyone who wants a real,
always-on radio stream without stitching five programs
together by hand. It is free and open-source software
(licensed under the AGPL, version 3), built to run — in the
project's own words — on even the most affordable VPS hosts.
Chapter 3 turns that claim into actual numbers.

What it asks of you is modest but real: a machine that runs
Docker, a web browser, and music you have the right to play.
Everything else, this guide takes in order.
