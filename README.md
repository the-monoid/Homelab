Setup for nextcloud on a bridged network running behind a reverse proxy.

SERVICES: 
    - pihole: local dns server
    - nextcloud-aio
    - caddy: reverse proxy
    - plex: media server

File structure:
    Project root directory.\n
    ├── caddy
    │   └── Caddyfile
    ├── docker-compose.yml
    ├── nextcloud
    ├── plex
    └── etc-pihole

docker-compose.yml:
    Unsure if this actually enables gpu access, and if so - to what containers. Important to note that this was added after container creation.





Build:
    MUST DISABLE SERVICE RUNNING ON PORT 53.
    For Ubuntu: 
        sudo systemctl stop systemd-resolved
        sudo systemctl disable systemd-resolved
        # check that services are not running
        lsof -i tcp:53
        lsof -i udp:53
    # Build docker image
    docker compose up -d
    # Containers will be starting.
    # navigate to http://<your-host-machine-ip>:8080/admin # or the https analogy for port 443
    # sign in with you password
In pihole: add your domain for nextcloud to your local dns records.
    Either set <your-host-machine-ip> as your dns server in your router, or 
    change the dns on your host machine to be <your-host-machine-ip>
    check that all is working: nslookup <your-cool-domain> (should return your hosts ip)
Nextcloud setup:
    navigate to https://<your-host-machine-ip>:8081
    Follow instructions on setup: https://github.com/nextcloud/all-in-one
    If domain check fails, CHECK LOGS for caddy and nextcloud-aio-mastercontainer.
    Likely issues, DNS is not working as expected. If you checked this previously with nslookup, then caddy is not communicating with nextcloud-aio-apache.

Plex:
    This should be working. However, you must access this from where plex is hosted, i.e., inside the docker. This is a problem.
    Possible solutions - open port 22, proxy from localhost to plex docker image. Set a accesstoken.
