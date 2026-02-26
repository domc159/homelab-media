# MediaServer

One-script self-hosted media stack for Ubuntu Server. Installs, configures, and starts a suite of media services via Docker with isolated networks.

## Services

| Service | Port | Description |
|---|---|---|
| [Jellyfin](https://jellyfin.org/) | `8060` | Media server for films, series & music |
| [Audiobookshelf](https://www.audiobookshelf.org/) | `8061` | Audiobook & podcast server |
| [Navidrome](https://www.navidrome.org/) | `8062` | Music streaming server |

## Requirements

- Ubuntu Server 22.04 / 24.04
- Root or `sudo` access

## Quick Start

```bash
curl -fsSL https://raw.githubusercontent.com/YOUR_USER/YOUR_REPO/main/install_media.sh -o install_media.sh
sudo bash install.sh
```

The script installs Docker, creates data directories, generates `/home/homelab/.env`, writes `docker-compose.yml`, and starts all containers.

## Directory Structure

| Path | Purpose |
|---|---|
| `/home/homelab/` | Compose file and `.env` |
| `/home/homelab_data/jellyfin/` | Jellyfin config and cache |
| `/home/homelab_data/audiobookshelf/` | Audiobookshelf config and metadata |
| `/home/homelab_data/navidrome/` | Navidrome database |
| `/home/data/media/` | Films and series (shared, read-only) |
| `/home/data/music/` | Music library (shared, read-only) |
| `/home/data/audiobooks/` | Audiobooks and podcasts (shared) |

Place your media files in `/home/data/{media,music,audiobooks}/` before or after installation. Jellyfin and Navidrome will pick them up on the next library scan.

## Configuration

Settings are stored in `/home/homelab/.env` (auto-generated, `chmod 600`). Notable defaults to review:

```dotenv
SERVER_IP=<auto-detected>
TZ=Europe/Ljubljana
JELLYFIN_PUBLISHED_SERVER_URL=http://<SERVER_IP>:8060
ND_SCANSCHEDULE=@every 1m
```

Optional API keys for Navidrome can be filled in after installation:

```dotenv
ND_SPOTIFY_ID=
ND_SPOTIFY_SECRET=
ND_LASTFM_APIKEY=
ND_LASTFM_SECRET=
```

Re-running the script will not overwrite an existing `.env`.

## Managing Services

```bash
cd /home/homelab
docker compose ps                             # status
docker compose logs -f <service>             # logs
docker compose pull && docker compose up -d  # update
docker compose down                          # stop all
```

## Notes

- Jellyfin takes 1-2 minutes to initialise on first start.
- The `.env` file contains secrets -- do not commit it to git.
- For external access, place services behind a reverse proxy (Caddy, Nginx Proxy Manager, Traefik) with valid TLS.

## Uninstall

```bash
cd /home/homelab && docker compose down -v
rm -rf /home/homelab /home/homelab_data /home/data
```
