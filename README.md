# StreamRec

A self-hosted live stream recorder. Supports Chaturbate, Twitch, YouTube, TikTok, and many more platforms via yt-dlp.

## Features

- Auto-detects when channels go live and starts recording
- Web UI to manage channels and recordings
- Supports proxies / VPN per channel or globally
- Cookies/credentials support for age-restricted streams
- Persists recordings across restarts

---

## Setup

### Requirements

- Docker + Docker Compose

### Install

```bash
git clone https://github.com/orhogi/streamerREC
cd streamerREC
docker compose up -d
```

Open `http://<your-server-ip>:8080`

---

## VPN / Proxy Setup

StreamRec includes a built-in WireGuard proxy (wireproxy) that runs as a sidecar container. It exposes a SOCKS5 proxy at `socks5://wireproxy:1080` that you can assign to any channel.

### 1. Add your WireGuard config

Place your WireGuard config file at:

```
streamerREC/wg0.conf
```

It should look like a standard WireGuard config:

```ini
[Interface]
PrivateKey = <your private key>
Address = 10.x.x.x/32
DNS = 1.1.1.1

[Peer]
PublicKey = <server public key>
Endpoint = <server>:<port>
AllowedIPs = 0.0.0.0/0
```

> You can get this from any WireGuard VPN provider (Mullvad, ProtonVPN, etc.) or your own server.

### 2. Rebuild with the VPN config

```bash
docker compose down && docker compose build && docker compose up -d
```

### 3. Assign the proxy to a channel

In the web UI:

1. Open a channel's settings
2. Set the **Proxy** field to:
   ```
   socks5://wireproxy:1080
   ```
3. Save

That channel will now record and check live status through the VPN.

### 4. Set a global proxy (optional)

To route **all** channels through the VPN:

1. Go to **Settings**
2. Set the **Proxy** field to:
   ```
   socks5://wireproxy:1080
   ```
3. Save

---

## Notes

- The proxy is opt-in — channels without a proxy set go direct
- Live detection also routes through the proxy, so channels that are geo-blocked will still be detected correctly
- If wireproxy fails to connect, check your `wg0.conf` and that the VPN endpoint is reachable
