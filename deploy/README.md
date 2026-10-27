# Deploying releases.devoltz.party

The site is static HTML. The map files are not on this host: they live in Cloudflare R2, served at dl.devoltz.party.

```
publica.py  -->  GitHub release vYYYYMMDD.HHMM of devoltzz/devoltz-releases with site-<tag>.zip + .sha256
releases-sync  --(public API, every 15 min)-->  /opt/releases-site/www/releases/<tag>, then current -> it
releases-site (nginx)  <--  the Cloudflare tunnel of devo-site  <--  visitors
```

## Once

1. Cloudflare R2: create a bucket (e.g. `devoltz-releases`), connect the custom domain `dl.devoltz.party` to it
   (R2 > bucket > Settings > Custom Domains), and create an API token with "Object Read & Write" on that bucket only.
   Put the keys in `produtos/releases/.env` on the acervo machine (never on the VPS, never in git).
2. GitHub: create the public repository `devoltzz/devoltz-releases` (empty, with a README), then on the acervo
   machine: `gh repo clone devoltzz/devoltz-releases externos/devoltz-releases`.
3. VPS: folder `/opt/releases-site/www`, owned by uid 10101 (written by releases-sync). Copy this `deploy/` folder
   to `/opt/releases-site/` and run `docker compose up -d` there (or the same file as a stack in Komodo).
4. The tunnel: in Cloudflare, add the public hostname `releases.devoltz.party` to the tunnel of the `devo-site`
   stack, pointing at `http://releases-site:80`. The zone settings the Doctor needs (Rocket Loader off, SSL Full)
   already hold.
5. Discord: in the forum channel of the maps, Integrations > Webhooks > New Webhook; put its URL in the `.env`
   (`DISCORD_WEBHOOK_URL`). With Developer Mode on, right-click each map's thread > Copy Thread ID, into the
   `discord_thread_id` of that map's `port/releases/mapa.json`.

## Each release

`python produtos/releases/publica.py <project> <version>` (only on the user's "publica"). releases-sync puts the new
site in place within 15 minutes (`docker logs releases-sync`). To go back: point `current` at an older folder in
`releases/`.

## What the server keeps

Nothing about visitors: nginx has no access log, there are no cookies and no metrics.
