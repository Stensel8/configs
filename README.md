# configs

Hardened server configuration files for reference and reuse.
All domain names and internal IPs have been replaced with `example.com` / `example2.com` and generic RFC-1918 addresses.

## Structure

```
configs/
├── nginx/
│   ├── nginx.conf                        # Main config — modules, TLS, compression, QUIC
│   ├── conf.d/
│   │   ├── 00-default-catch-all.conf     # Default vhost: 418 teapot for unknown hosts
│   │   ├── admin.example.com.conf        # Cockpit admin UI (TLS 1.3 only)
│   │   ├── bazarr.example.com.conf       # Bazarr subtitle manager
│   │   ├── home.example.com.conf         # Home Assistant
│   │   ├── jellyfin.example.com.conf     # Jellyfin media server
│   │   ├── lidarr.example.com.conf       # Lidarr music collection manager
│   │   ├── minecraft.example.com.conf    # Minecraft (redirects to status page)
│   │   ├── music.example.com.conf        # Navidrome music streaming
│   │   ├── prowlarr.example.com.conf     # Prowlarr indexer manager
│   │   ├── qbittorrent.example.com.conf  # qBittorrent WebUI
│   │   ├── radarr.example.com.conf       # Radarr movie collection manager
│   │   ├── seerr.example.com.conf        # Jellyseerr request manager
│   │   ├── sonarr.example.com.conf       # Sonarr series collection manager
│   │   └── status.example2.com.conf      # Uptime Kuma status page (secondary domain)
│   └── stream.d/                         # TCP/UDP stream proxies (add as needed)
│
├── ssh/
│   └── sshd_config                       # Hardened OpenSSH — Ed25519, ML-KEM, no passwords
│
└── podman/                               # Quadlet container units (add as needed)
```

## Highlights

### nginx
- nginx 1.31+ with HTTP/2, HTTP/3 (QUIC), zstd + gzip compression
- Global TLS: TLS 1.2/1.3, ECDSA ciphers, X25519MLKEM768 key exchange
- Dynamic modules: `headers-more`, `zstd`, `ngx_http_acme_module`
- All reverse-proxy vhosts disable compression (`zstd off; gzip off`) to avoid
  conflicts with upstream encoding

### OpenSSH
- Ed25519 host keys and client keys only — no RSA, no ECDSA
- ML-KEM 768 + curve25519 key exchange (post-quantum hardened)
- Password authentication disabled — public key only
- All forwarding (TCP, agent, X11, tunnel) disabled
