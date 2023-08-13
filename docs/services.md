# Software & Services
## Table of contents
- [Management](#management)
  - [GoDaddy](#godaddy)
  - [Cloudflare](#cloudflare)
  - [Portainer](#portainer)
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
  
# Management
## GoDaddy


## Cloudflare


## Portainer
Portainer is an open-source management platform designed to simplify the deployment and management of containerized applications and infrastructure. It provides an intuitive web-based interface that allows users to easily interact with and manage Docker containers, Kubernetes clusters, and other container orchestration tools. Portainer enables both beginners and experienced developers to streamline the containerization process and monitor their applications with ease.
![img](https://www.portainer.io/hubfs/portainer-logo-white.svg)
### *Install*
After installing [docker and docker-compose](#docker) create a volume
```
docker volume create portainer_data
```
and then create the container.
```
docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
Chosing to utilize the legacy http port 9000 for conviniance reasons, as the https connection will not matter as any exposure to the web will be done with [Cloudflare Tunnel](#cloudflare-tunnel).

To access the Portainer gui go to ```http://serverIP:9000```. There you will be asked to make an admin account. And thats it, you are good to go!

# Utils
## RAID


## Cron


## Docker


## Cloudflare Tunnel


## Grafana


# Services
## Nextcloud


## Jellyfin


## Website


