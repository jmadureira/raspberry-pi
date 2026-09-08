# raspberry-pi

Docker services I run on my Raspberry Pi. Each service has its own directory and
its own compose file, so they start, stop and upgrade independently.

| Service | Directory | Port | What it does |
| --- | --- | --- | --- |
| [Pi-hole](https://pi-hole.net) | `.` | [8080](http://raspberrypi.local:8080/admin) | Network-wide DNS ad blocking |
| [Navidrome](https://www.navidrome.org) | [`music/`](music/README.md) | [4533](http://raspberrypi.local:4533) | Music streaming over the local network |

Setup for each service lives in its own directory: see
[`music/README.md`](music/README.md).
