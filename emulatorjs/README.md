# EmulatorJS

A self-hosted [EmulatorJS](https://emulatorjs.org) game library, served by nginx
on port [8085](http://raspberrypi.local:8085).

## ROMs on a USB drive

ROMs are read from `ROMS_DIR` (default `./data/roms`), one folder per system named
after the EmulatorJS core:

```
/mnt/games/
├── roms/
│   ├── nes/
│   ├── snes/
│   └── psx/
└── bios/
    └── psx/scph5501.bin
```

### 1. Mount the drive at a fixed path

Raspberry Pi OS only auto-mounts USB drives under `/media/<user>/` when someone is
logged into the desktop, so on a headless Pi mount it through `/etc/fstab`
instead. Plug the drive in and find its UUID and filesystem type:

```sh
lsblk -f
```

Create the mount point and add a line to `/etc/fstab` (`sudo nano /etc/fstab`),
using the UUID and type from above. For an **exFAT** or **FAT32** drive (`exfat` /
`vfat`):

```
UUID=1234-ABCD  /mnt/games  exfat  defaults,nofail,x-systemd.before=docker.service,x-systemd.device-timeout=10s,uid=1000,gid=1000,umask=022  0  0
```

For an **ext4** drive, drop the `uid`, `gid` and `umask` options (ext4 keeps its own
file permissions) and make sure the ROMs are world-readable (`chmod -R a+rX`).

- `nofail` lets the Pi boot normally when the drive is unplugged.
- `x-systemd.before=docker.service` mounts the drive before Docker starts the
  container, so it doesn't start against an empty folder.
- `x-systemd.device-timeout=10s` stops a missing drive from holding up Docker
  (and Pi-hole) for the default 90 seconds.
- `umask=022` makes files readable by nginx inside the container, which runs as
  a different user.

Then mount it and check it worked:

```sh
sudo mkdir -p /mnt/games
sudo systemctl daemon-reload
sudo mount -a
ls /mnt/games
```

### 2. Point the container at it

```sh
cp .env.example .env    # set ROMS_DIR and BIOS_DIR
docker compose up -d --build
```

If the drive isn't mounted when the container starts, it fails to start instead
of showing an empty library. Plug the drive in, `sudo mount -a`, and run
`docker compose up -d` again. The same applies after unplugging and replugging
the drive while the container is running: restart it with
`docker compose restart` so it picks up the new mount.

## Game formats

EmulatorJS downloads a single file per game, so multi-track or multi-disc games
(`.cue` + several `.bin`) need to be a `.zip` or `.chd`. Loose `.cue`, `.m3u` and
extra track files are hidden from the library.
