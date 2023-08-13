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
sudo docker volume create portainer_data
```
and then create the container.
```
sudo docker run -d -p 8000:8000 -p 9000:9000 --name=portainer --restart=always --network=general -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest
```
Chosing to utilize the legacy http port 9000 for conviniance reasons, as the https connection will not matter as any exposure to the web will be done with [Cloudflare Tunnel](#cloudflare-tunnel).

To access the Portainer gui go to ```http://serverIP:9000```. There you will be asked to make an admin account. And thats it, you are good to go!

# Utils
## RAID


## Cron


## Docker


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

![img](/img/tunnel3.png)

## Grafana


# Services
## Nextcloud


## Jellyfin


## Website


