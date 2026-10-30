# Writing criteria for this guide

Every "for Mere Mortals" guide is written to the same nine-point
standard, so a reader who trusts one can trust the rest. Here it
is, with the verification notes for this particular guide below.

1. **Fix it first.** Reader outcomes lead, not action items: each
   chapter opens by naming the outcome it delivers, shows a check
   the reader can run to confirm they got it, and a reasonable
   reader finishes able to state what was done, what it means,
   and what to try next.
2. **Plain language.** Short sentences, everyday vocabulary,
   technical terms defined on first use. No slang, and no
   idioms that only make sense in one country — the reader may
   be translating as they go.
3. **No dead ends.** A guide never tells the reader to do
   something it cannot show them how to verify. Every claim a
   reader relies on — an install, a command, an output — is
   something this guide demonstrates or clearly labels
   documented-only below.
4. **Live-verified where it matters.** Procedures are run for
   real in the guide's lab wherever the build machine allows it,
   and the transcripts kept in the repository are the evidence.
   What could not be run is listed honestly below, never
   presented as if it had been.
5. **General audiences.** Nothing in this guide — examples,
   fixtures, names, addresses — is modelled on any particular
   reader or author's real life, network, or organisations.
   The example station is an invented one.
6. **Standalone.** A reader needs this guide, its lab, and
   nothing else. Where another guide would genuinely help, it
   is pointed to once, by name, and the chapter still completes
   without it.
7. **Honest about limits.** Chapter 12 names what the tool
   cannot do: a Docker-only life, one container holding
   everything, generated configuration that is not yours to
   edit, the port bill, and the licensing it cannot provide.
8. **The ethics rule is in the text, early.** Chapter 1 states
   plainly that broadcasting recorded music to the public is
   a licensed activity in most countries, that AzuraCast
   includes no such licences, and that this guide is not
   legal advice; chapter 9 adds the listener-data corollary,
   and chapter 12 returns to both.
9. **Builds clean.** The site builds with MkDocs in strict mode
   with no warnings, and a fresh reader following the chapters
   in order reaches the stated outcome of each.

## Verification notes for this guide

**This guide's core is documented-only, and says so wherever
it matters.** AzuraCast's only supported installation runs
under Docker Compose, and this lab's machine has no Docker
daemon and cannot run containers, so AzuraCast was never
installed or started here. No output in the guide is
presented as a transcript of a running installation.

In place of running it, the lab **dissected the project's
own artefacts** — a shallow clone of
https://github.com/AzuraCast/AzuraCast at commit
`a683c9dbb09ddab3fd6eec6793e691dc0575ff57` (2026-10-26;
latest stable in tree 0.24.0, per `CHANGELOG.md` and
`backend/src/Version.php`) — and checked the workflows
against the official documentation at azuracast.com, read
2026-10-30. The full working notes are in
`azuracast-lab/dissection-notes.md`; the clone itself was
deleted after dissection.

Verified from the project's own files:

- The delivered Compose file (`docker-compose.sample.yml`):
  two services (the all-in-one `web` container and the
  `updater`), the published ports (80, 443, 2022, and radio
  ports 8000–8496 in the base / base+5 / base+6 pattern),
  the named volumes, and the image tags behind the
  Rolling/Stable channels.
- Port assignment logic in `backend/src/Radio/Configuration.php`:
  blocks of ten searched from 8000; frontend port = block
  base; DJ port = base + 5; Liquidsoap telnet port =
  base + 4 (not forwarded).
- The Liquidsoap harbor in
  `backend/src/Radio/Backend/Liquidsoap/ConfigWriter.php`
  (`input.harbor` with the `azuracast.dj_auth` function),
  and the playlist code paths behind chapter 6's rules
  (weighted `random()` for rotation, time-based `switch()`
  for scheduled playlists, `input.http()` for remote
  streams).
- `docker.sh` (953 lines): the install / update /
  update-self / backup / restore / rollback /
  change-ports / letsencrypt functions and the install
  requirement checks, read directly.
- The environment anatomy: `azuracast.sample.env` and
  `sample.env` (port range 8000–8499 defaults, the
  do-not-change-the-database-password warning).
- The API: the shipped OpenAPI specification
  (`web/static/openapi.yml`, OpenAPI 3.0.0, version
  0.24.0) — the public `security: []` endpoints, the
  `X-API-Key` header scheme, and the `Api_NowPlaying`
  schema field by field. The worked JSON example in
  chapter 10 is assembled from those schema fields, with
  invented values, and is labelled in the text as a map,
  not a captured response.

Documented only — described from the project's official
documentation, not run, and the text does not claim
otherwise:

- Everything past the installer's finish line: the setup
  wizard's screens, station creation, the media manager,
  playlist and schedule editing, the station profile page,
  and the Web DJ (docs: installation, Docker administration,
  station management pages).
- Going live from BUTT and Mixxx, whose settings are taken
  from the project's streaming-software page.
- Remote relays and AzuraRelay (docs: remote relays page).
- Listener reports and the SoundExchange-format export
  (docs: feature list).
- The one-click installers at VPS providers, which are
  other companies' panels entirely.

Fixtures are this guide's own and deliberately generic: an
invented station ("Example FM"), invented songs by invented
artists, and the documentation's own example domain. No
real station, server, or listener appears anywhere in it.
