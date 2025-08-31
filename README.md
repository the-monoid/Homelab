Here is a setup guide for Nextcloud All-in-One (AIO) on a bridged network behind a reverse proxy, along with other services like Pi-hole, Plex, and Caddy.

## Services and File Structure

This guide uses the following services:

* **Pi-hole:** A local DNS server to manage internal network resolution.
* **Nextcloud AIO:** The all-in-one containerized Nextcloud instance.
* **Caddy:** A reverse proxy to handle SSL/TLS termination and route traffic to the correct services.
* **Plex:** A media server.

The suggested project file structure is as follows:

├── caddy

│       └── Caddyfile

├── docker-compose.yml

├── nextcloud

├── plex

└── etc-pihole

---

## Disable `resolved` on Ubuntu

Before building your Docker containers, you must disable the service running on port 53 to allow Pi-hole to function as your DNS server.

1.  **Stop the service:**
    ```bash
    sudo systemctl stop systemd-resolved
    ```
2.  **Disable the service permanently:**
    ```bash
    sudo systemctl disable systemd-resolved
    ```
3.  **Remove the symlink and create a new `resolv.conf` file:**
    ```bash
    sudo rm /etc/resolv.conf
    sudo nano /etc/resolv.conf
    ```
    Add a public DNS server like Google's to the new file (e.g., `nameserver 8.8.8.8`).
4.  **Verify port 53 is free:**
    ```bash
    lsof -i tcp:53
    lsof -i udp:53
    ```
    You should see no output, indicating that no services are listening on these ports.

---

## Docker Compose and Build

With `resolved` disabled, you can now build your Docker containers.

1.  **Create your `docker-compose.yml` file:** Define your services, networks, and volumes in this file. Ensure you configure the network settings correctly for a **bridged network** to allow communication between containers and with the host. BE SURE TO ADD A VALID ACCESS TOKEN TO PLEX SERVICE.
2.  **Build the containers:**
    ```bash
    docker compose up -d
    ```
    This command builds and starts all the services defined in your `docker-compose.yml` file in detached mode.

---

## Pi-hole Setup

Once the containers are running, you need to configure Pi-hole to handle DNS for your domain.

1.  **Navigate to the Pi-hole admin interface:** Access the web interface at `http://<your-host-machine-ip>/admin`.
2.  **Add a local DNS record:** Go to **Local DNS > DNS Records** and add your Nextcloud domain. The domain should point to your host machine's IP address. For example:
    * **Domain:** `nextcloud.yourdomain.com`
    * **IP Address:** `<your-host-machine-ip>`
3.  **Set your host's DNS:** Change the DNS server on your host machine to its own IP address (`<your-host-machine-ip>`) to use Pi-hole for name resolution. Alternatively, you can configure your router to use the host's IP as its primary DNS.
4.  **Verify DNS resolution:** Use `nslookup` to confirm your domain resolves correctly.
    ```bash
    nslookup nextcloud.yourdomain.com
    ```
    The output should return your host machine's IP address.

---

## Nextcloud AIO Setup

Now you can proceed with the Nextcloud AIO setup.

1.  **Access the Nextcloud AIO installer:** Navigate to the installer at `https://<your-host-machine-ip>:8080`.
2.  **Follow the setup instructions:** The installer will guide you through the process, including setting up the domain and a reverse proxy. See [Nextcloud AIO GitHub repository](https://github.com/nextcloud/all-in-one "Nextcloud All-in-One")
3.  **Troubleshooting:** If the domain check fails, inspect the logs for both the `caddy` and `nextcloud-aio-mastercontainer` services. A common issue is DNS misconfiguration or Caddy not properly communicating with the Nextcloud AIO Apache container.

---

## Plex Setup

Plex should be accessible:

** Setup Plex: ** http://localhost:32400/web brings you to plex.
