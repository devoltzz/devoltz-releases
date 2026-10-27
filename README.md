# devoltz-releases

The site of [releases.devoltz.party](https://releases.devoltz.party): the changelog, the decisions and the downloads
of every Warcraft III map ported to Reforged by devoltz.

Each GitHub release here carries one build of the site (`site-<tag>.zip` and its `.sha256`); the host picks up the
latest one by itself (`deploy/sync.py`). The map files are served from dl.devoltz.party, not from this repository.
`deploy/` holds what runs on the host: nginx (no access log), the headers (a strict Content-Security-Policy) and the
sync container.
