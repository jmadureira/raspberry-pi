# Navidrome

[Navidrome](https://www.navidrome.org) streams my music library over the local
network. Web player plus the Subsonic API, so phone clients work too.

## Setup

Copy the example environment file and point `MUSIC_DIR` at the music library on
the Pi:

```sh
cp .env.example .env
```

`.env.example` documents every setting; the one that always needs changing is
`MUSIC_DIR`. `TZ`, `PUID`/`PGID` and `NAVIDROME_PORT` fall back to sensible
defaults (`Europe/Lisbon`, `1000:1000`, `4533`) if you leave them alone. Use
`id -u` and `id -g` on the Pi to check the uid/gid — they need to read the music
library and write `data/`.

Start it:

```sh
docker compose up -d
```

Then open http://raspberrypi.local:4533 and create the admin account. The first
scan takes a while on a large library; watch it with `docker compose logs -f`.

The library is mounted read-only, so Navidrome can never modify or delete the
original files.

## Upgrading

The image is pinned to an exact version rather than `latest`, so a `docker
compose pull` never changes what is running behind my back. To upgrade, bump the
tag in `docker-compose.yml` after checking the
[release notes](https://github.com/navidrome/navidrome/releases):

```sh
docker compose pull && docker compose up -d
```

Navidrome migrates its database on start, and migrations are not reversible —
copy `data/navidrome.db` somewhere safe first if the jump is a big one.

## Clients

Navidrome serves a web player and implements the Subsonic API, so any Subsonic
client works:

- **iPhone** — [Amperfy](https://apps.apple.com/app/amperfy-music/id1530145038) (free, open source) or [play:Sub](https://apps.apple.com/app/play-sub-music-streamer/id955329386) (paid)
- **Android** — [Symfonium](https://symfonium.app) (paid, the nicest of the bunch) or [Tempo](https://github.com/CappielloAntonio/tempo) (free)
- **Desktop** — the built-in web player, or [Feishin](https://github.com/jeffvli/feishin) for a native app

Point clients at `http://<pi-ip>:4533` with the username and password created
above.
