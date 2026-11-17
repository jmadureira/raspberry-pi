# Pi-hole

[Pi-hole](https://pi-hole.net) blocks ads and trackers for every device on the
network by acting as its DNS server.

## Setup

Set the admin password before the first start: `FTLCONF_webserver_api_password`
in `docker-compose.yml` defaults to `password`. You can also change it later
with:

```sh
docker exec -it pihole pihole setpassword
```

Pi-hole answers DNS on port 53 of the Pi. If the container fails to start
because that port is already taken (`sudo ss -lunp 'sport = :53'`), it's
usually `systemd-resolved`. Set `DNSStubListener=no` in
`/etc/systemd/resolved.conf` and run `sudo systemctl restart systemd-resolved`.

Start it:

```sh
docker compose up -d
```

The admin UI is at http://raspberrypi.local:8080/admin.

## Using it

Set the Pi's IP address as the DNS server in the router's DHCP settings, so
every device picks it up automatically. To try it on a single device first, set
that device's DNS server to the Pi's IP instead.

## Upgrading

```sh
docker compose pull && docker compose up -d
```

Settings and the query database live in `data/etc-pihole` and survive upgrades.
