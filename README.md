Here is a setup guide for Nextcloud All-in-One (AIO) on a bridged network behind a reverse proxy, along with other services like Pi-hole, Plex, and Caddy.

## Services and File Structure

This guide uses the following services:

* **Pi-hole:** A local DNS server to manage internal network resolution.
* **Nextcloud AIO:** The all-in-one containerized Nextcloud instance.
* **Caddy:** A reverse proxy to handle SSL/TLS termination and route traffic to the correct services.
* **Plex:** A media server.

The suggested project file structure is as follows:

├── caddy

│   └── Caddyfile

├── docker-compose.yml

├── nextcloud

├── plex

└── etc-pihole
