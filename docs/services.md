# Software & Services
## Table of contents
- [Management](#management)
  - [GoDaddy](#godaddy)
  - [Cloudflare](#cloudflare)
  - [Portainer](#portainer)
  - [Nginx Proxy Manager](#nginx-proxy-manager)
- [Utils](#utils)
  - [RAID](#raid)
  - [Cron](#cron)
  - [Docker](#docker)
  - [Cloudflare Tunnel](#cloudflare-tunnel)
  - [Grafama](#grafana)
- [Services](#services)
  - [Nextcloud](#nextcloud)
  - [Jellyfin](#jellyfin)
  - [Website](#website)

 ---
  
# Management
## GoDaddy

---

## Cloudflare

---

## Portainer
Portainer is an open-source management platform designed to simplify the deployment and management of containerized applications and infrastructure. It provides an intuitive web-based interface that allows users to easily interact with and manage Docker containers, Kubernetes clusters, and other container orchestration tools. Portainer enables both beginners and experienced developers to streamline the containerization process and monitor their applications with ease.
![img](https://www.portainer.io/hubfs/portainer-logo-white.svg)
### *Install*
After installing [docker and docker-compose](#docker) create a volume
```
sudo docker volume create portainer_data
```
and then create the container.
```
sudo docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always --network=general -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
Chosing to utilize the legacy http port 9000 for conviniance reasons, as the https connection will not matter as any exposure to the web will be done with [Cloudflare Tunnel](#cloudflare-tunnel).

To access the Portainer gui go to ```http://serverIP:9000```. There you will be asked to make an admin account. And thats it, you are good to go!

---

## Nginx Proxy Manager
Nginx Proxy Manager is a web-based application designed to simplify the process of managing reverse proxy configurations using the Nginx web server. It provides a user-friendly interface for users to create, modify, and manage proxy rules that enable routing incoming web traffic to various backend services, such as web applications or APIs. Nginx Proxy Manager streamlines the typically complex configuration tasks associated with setting up reverse proxies, making it accessible to users with varying levels of technical expertise. Through its intuitive dashboard, users can easily create custom domain configurations, set up SSL certificates, manage routing rules, and monitor traffic flow, all without directly dealing with intricate Nginx configuration files. This tool is particularly useful for individuals or organizations aiming to efficiently and securely expose multiple web services using a single public IP address.

![img](https://nginxproxymanager.com/github.png)
### *Install*
Utilizing the proposed docker-compose file with some minor additions provided by the [official documentation](https://nginxproxymanager.com/guide/#quick-setup) through the [Portainer](#portainer) Stacks deployment feature:
```
version: '3.8'
networks:
  general:
    external: true
services:
  app:
    image: 'jc21/nginx-proxy-manager:latest'
    networks:
      - general
    restart: always
    ports:
      # These ports are in format <host-port>:<container-port>
      - '80:80' # Public HTTP Port
      - '443:443' # Public HTTPS Port
      - '81:81' # Admin Web Port
      # Add any other Stream port you want to expose
      - '3478:3478' # Nextcloud Talk
    environment:
      # Mysql/Maria connection parameters:
      DB_MYSQL_HOST: "npmdb"
      DB_MYSQL_PORT: 3306
      DB_MYSQL_USER: "npm"
      DB_MYSQL_PASSWORD: "[password]"
      DB_MYSQL_NAME: "npm"
    volumes:
      - ./data:/data
      - ./letsencrypt:/etc/letsencrypt
    depends_on:
      - npmdb

  npmdb:
    image: 'jc21/mariadb-aria:latest'
    networks:
      - general
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: '[password]'
      MYSQL_DATABASE: 'npm'
      MYSQL_USER: 'npm'
      MYSQL_PASSWORD: '[password]'
    volumes:
      - ./mysql:/var/lib/mysql
```
Then in Portainer:

![img](/img/ngxpm1.png)
![img](/img/ngxpm2.png)
![img](/img/ngxpm3.png)

Then click the "Deploy the stack" at the bottom of the page and you are done.

You should now be able to access the managment page at ```yourServerIP:81/``` where you need to log in with the default username and password:
```
Email:    admin@example.com
Password: changeme
```

![img](/img/ngxpm4.png)

You will then be asked to change the default user and create a new password. Now the settup process is complete.

---

# Utils
## RAID

---

## Cron

---

## Docker

---

## Cloudflare Tunnel
Cloudflare Tunnel, formerly known as "Argo Tunnel," is a service provided by Cloudflare that securely connects your infrastructure to the Cloudflare network. It allows you to route traffic from your servers, data centers, or private networks through Cloudflare's global network of data centers. This offers several benefits, including enhanced security, performance optimization, and simplified network management.

### *Install*
After seting up your Cloudflare account and domain you can go your home page and chose "Zero Trust" from the side menu.
Next under the "Access" dropdown chose "Tunnels" and chose you plan (free).

![img](/img/tunnel1.png)

Then you should be ready to add a new tunnel. Proceede to name your tunnel and choose your prefered method of installation, I chose the docker install.

![img](/img/tunnel2.png)

Then though the docker CLI we innitiate our connector on the same network as the services you want to expose (replacing the placeholder with your token).
```
sudo docker run -d --restart=always --name=tunnel --network=general cloudflare/cloudflared:latest tunnel --no-autoupdate run --token [superSecretToken]
```
If all was sucessfull you should see

![img](/img/tunnel3.png)

### *Expose a service to the web*
In the tunnel configuration page chose the "Public hostanames" tab there you need to click on "Add a public hostname" button. Then fill in your details in this example I am going to expose my portainer instance, using docker network name recognition I can simply type in the container name in the url field. Lastly click "Save" and you should be all good.

![img](/img/tunnel4.png)

---

## Grafana

---

# Services
## Nextcloud
Nextcloud is an open-source, self-hosted cloud collaboration platform that enables individuals and organizations to store, share, and manage files, documents, calendars, contacts, and more. It offers features similar to popular cloud services, such as file synchronization, data access from various devices, secure sharing with others, and collaborative tools like document editing and real-time collaboration. Nextcloud prioritizes data privacy and security, allowing users to maintain control over their data by hosting the platform on their own servers or choosing trusted providers. This provides a flexible and customizable solution for those seeking an alternative to commercial cloud storage services while maintaining control over their digital assets.

![img](https://frappecloud.com/files/nextcloud-square-logo.png)

I will be primarilly using this for cloud storage for me and my family, but some of the other features are also nice to have.

### *Install*
I will be installing the Nextcloud All-in-One [docker](#docker) image utilizing [Portainer](#Portainer) which i have set up previously. Following the [official documentation](https://github.com/nextcloud/all-in-one) I am going to be using a [premade docker compose script](https://github.com/nextcloud/all-in-one/blob/main/compose.yaml) they have made for Portainer with some minor difrances like removing commented out extras. Pasting this docker-compose script just like in the [Nginx Proxy Manager](#nginx-proxymanager) install. Deploying it with Cloudflare Tunnel comes with some major downsides according to the docs, so I will deploy it with Nginx Proxy Manager instead.

```
volumes:
  nextcloud_aio_mastercontainer:
    name: nextcloud_aio_mastercontainer # This line is not allowed to be changed as otherwise the built-in backup solution will not work
networks:
  general:
    external: true
services:
  nextcloud-aio-mastercontainer:
    image: nextcloud/all-in-one:latest
    networks:
      - general
    restart: always
    container_name: nextcloud-aio-mastercontainer # This line is not allowed to be changed as otherwise AIO will not work correctly
    volumes:
      - nextcloud_aio_mastercontainer:/mnt/docker-aio-config # This line is not allowed to be changed as otherwise the built-in backup solution will not work
      - /var/run/docker.sock:/var/run/docker.sock:ro # May be changed on macOS, Windows or docker rootless. See the applicable documentation. If adjusting, don't forget to also set 'WATCHTOWER_DOCKER_SOCKET_PATH'!
    ports:
      - 8080:8080
    environment: # Is needed when using any of the options below
      - APACHE_PORT=11000 # Is needed when running behind a web server or reverse proxy (like Apache, Nginx, Cloudflare Tunnel and else). See https://github.com/nextcloud/all-in-one/blob/main/reverse-proxy.md
      - NEXTCLOUD_DATADIR=/media/nextcloud # Allows to set the host directory for Nextcloud's datadir. ⚠️⚠️⚠️ Warning: do not set or adjust this value after the initial Nextcloud installation is done! See https://github.com/nextcloud/all-in-one#how-to-change-the-default-location-of-nextclouds-datadir
      - NEXTCLOUD_MOUNT=/media/ # Allows the Nextcloud container to access the chosen directory on the host. See https://github.com/nextcloud/all-in-one#how-to-allow-the-nextcloud-container-to-access-directories-on-the-host
```
Now before proceding with the setup you need to add the host to the reverse proxy as the settup will need acces to the internet to do a domain check. In the managment page for Nginx Proxy Manager at ```yourServerIP:81/``` add a new proxy host:

![img](/img/nextcloud1.png)

![img](/img/nextcloud2.png)

![img](/img/nextcloud3.png)

![img](/img/nextcloud4.png)
```
client_body_buffer_size 512k;
proxy_read_timeout 86400s;
client_max_body_size 0;
```

And now asuming your proxy is set up correctly and the proper ports on your router are open everything should be ready to continue setup.


---

## Jellyfin

---

## Website

---
