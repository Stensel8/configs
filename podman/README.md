# Podman Quadlets

Rootless Podman container units managed by systemd via the [Quadlet](https://docs.podman.io/en/latest/markdown/podman-systemd.unit.5.html) interface.

## File location

Place these files in `~/.config/containers/systemd/` for user-level services, or `/etc/containers/systemd/` for system-level services.

```
~/.config/containers/systemd/
├── arr-stalled-handler.container
├── bazarr.container
├── flaresolverr.container
├── jellyfin.container
├── lidarr.container
├── minecraft.container
├── prowlarr.container
├── qbittorrent.container
├── radarr.container
├── recyclarr.container
├── recyclarr.timer
├── seerr.container
├── signoz.container
├── snowflake-proxy.container
├── sonarr.container
└── uptime-kuma.container
```

## Enable linger

By default, user systemd services are stopped when the user logs out. Enable linger so containers keep running after logout (e.g. on a headless server):

```bash
loginctl enable-linger $USER
```

Verify:

```bash
loginctl show-user $USER | grep Linger
# Linger=yes
```

To disable:

```bash
loginctl disable-linger $USER
```

## Managing services

After adding or changing a `.container` file, reload the daemon and start the service:

```bash
systemctl --user daemon-reload
systemctl --user start <name>.service
```

Check status:

```bash
systemctl --user status <name>.service
journalctl --user -u <name>.service -f
```

## Notes

- All containers use `AutoUpdate=registry` — run `podman auto-update` (or enable the `podman-auto-update.timer`) to pull new images automatically.
- Containers on `torrent-network` communicate by container name (e.g. `http://systemd-radarr:7878`). Create the network once with:
  ```bash
  podman network create torrent-network
  ```
- `arr-stalled-handler.container` requires Radarr and Sonarr API keys — set these via environment or a secrets file; do not commit real keys.
- `signoz.container` runs [SigNoz Standalone](https://hub.docker.com/r/signoz/signoz-standalone) on port `3301` (UI) and `14317`/`14318` (OTLP). Note: `Privileged=` is not supported in Quadlet; the standalone image runs without it.
