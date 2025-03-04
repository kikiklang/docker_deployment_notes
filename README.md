# Introduction
Caddy v2 is the easiest reverse proxy ever! You'll be able to host multiple dockerized applications with one VM and one domain name! I learned from [DoTheEvo](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/caddy_v2), but their guide assumes you already installed docker and docker-compose. From my [A-Z guide](https://github.com/StarWhiz/docker_deployment_notes/tree/master/initial%20ubuntu%20setup)... you be handheld from the beginning. In addition to the app specific guides on DoTheEvo's page I also added some of my own!

Anyways please start with the [A-Z Guide](https://github.com/StarWhiz/docker_deployment_notes/tree/master/initial%20ubuntu%20setup) guide below which will take you from base Ubuntu 20.04 to having docker, docker-compose, caddy v2 installed.

## A to Z Guide (Start Here)
https://github.com/StarWhiz/docker_deployment_notes/tree/master/initial%20ubuntu%20setup

After you finish the A-Z guide. You can then follow application specific guides from me below. Or from [DoTheEvo](https://github.com/DoTheEvo/selfhosted-apps-docker) for other apps.

## Application Specific Deployment Guides

#### DoTheEvo's App Specific Guides
* [caddy_v2](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/caddy_v2) - reverse proxy
* [bitwarden_rs](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/bitwarden_rs) - password manager
* [bookstack](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/bookstack) - notes and documentation
* [borg_backup](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/borg_backup) - backup utility
* [ddclient](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/ddclient) - automatic DNS update
* [dnsmasq](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/dnsmasq) - DNS and DHCP server
* [homer](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/homer) - homepage
* [nextcloud](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/nextcloud) - file share & sync
* [portainer](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/portainer) - docker management
* [prometheus_grafana](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/prometheus_grafana) - monitoring
* [unifi](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/unifi) - management utility for ubiquiti devices
* [watchtower](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/watchtower) - automatic docker images update
* [wireguard](https://github.com/DoTheEvo/selfhosted-apps-docker/tree/master/wireguard) - the one and only VPN to ever consider


#### StarWhiz's App Specific Guides
* [Heimdall](https://github.com/StarWhiz/docker_deployment_notes/tree/master/heimdall) - Another Homepage Dashboard
* [FreePBX+Asterisk](https://github.com/StarWhiz/docker_deployment_notes/tree/master/freepbx-asterisk) - VOIP & telephony server
* [Jellyfin](https://github.com/StarWhiz/docker_deployment_notes/tree/master/jellyfin) - Open Source Plex / Media Server
* [Jitsi Meet](https://github.com/StarWhiz/docker_deployment_notes/tree/master/jitsi-meet) - Videoconferencing
* [Mumble](https://github.com/StarWhiz/docker_deployment_notes/tree/master/mumble) - Voice Chat Before Discord Days
* [PureFTPd](https://github.com/StarWhiz/docker_deployment_notes/tree/master/pureftpd) - FTP server
* [qBit+windscribe](https://github.com/StarWhiz/docker_deployment_notes/tree/master/qbit-windscribe) - Torrent Client w/ Windscribe VPN (Issue: Storage Leaks)
* [Rocket.chat](https://github.com/StarWhiz/docker_deployment_notes/tree/master/rocketchat) - Discord / Slack Clone
* [ShinobiCCTV (Not Recommended)](https://github.com/StarWhiz/docker_deployment_notes/tree/master/shinobi) - CCTV NVR
* [Seafile](https://github.com/StarWhiz/docker_deployment_notes/tree/master/seafile) - Cloud Drive
* [Snipe IT](https://github.com/StarWhiz/docker_deployment_notes/tree/master/snipeit) - IT Asset Management
* [Ubiquiti UniFi Controller](https://github.com/StarWhiz/docker_deployment_notes/tree/master/unifi-controller) - Management Utility for Ubiquiti Devices
* [Wordpress](https://github.com/StarWhiz/docker_deployment_notes/tree/master/wordpress) - CMS / Website Hosting
* [Zoneminder](https://github.com/StarWhiz/docker_deployment_notes/tree/master/zoneminder) - CCTV NVR

#### Other App Specific References
* [140+ Docker Scripts by technorabilia](https://github.com/technorabilia/docker-bits)
* [More docker-compose files by bearlikelion](https://github.com/bearlikelion/docker-compose)
* [awesome-compose](https://github.com/docker/awesome-compose)
# Quick-References and Notes

## Most Commonly Used Commands
```
docker network create caddy_net # Only need to do this once on new VMs (From Step 9 of A-Z Guide)
```
Only need once.
```
docker exec -w /etc/caddy caddy caddy reload
```
Use this command everytime you make changes to Caddyfile.
```
docker exec -ti CONTAINERNAME /bin/bash
docker exec -ti CONTAINERNAME /bin/sh
```
Commands to enter a container's shell. Use bash first, if that doens't work try sh.

## Commonly used Docker Specific Commands
```
docker container ls     # list all running containers
docker container ls -a  # list all containers even stopped ones
docker container rm     # remove container
docker container kill   # kill a running container
docker system prune     # Remove all unused docker: containers, images, networks and volumes to free up space
docker container prune  # Similar to system prune but only targets containers.

### While inside app specific folder
docker-compose restart  # restart docker stack for application
docker-compose down     # turn off application
docker-compose up       # turn on application with logs. CTRL+C to exit
docker-compose up -d    # turn on application without logs and runs in background
docker-compose pull     # update application
```

## Commonly added lines added to app specific docker-compose.yml files
```
services:
  exampleapp:
    ### Commonly Added To Applications
    container_name: app-name  # A container name for Caddyfile to reference to 
                              # with reverse-proxy app-name:80 for example
    restart: unless-stopped   # When your VM restarts the docker-container will start automatically
    ports:
      - "8080:80"             # Usually reverse-proxy will do. Sometimes there are cases where you need ports open.
                              # Here we open port 8080 on the host machine. It maps to port 80 inside the docker container.
    volumes:
      - ./folder:/some/pathinsidecontainer/folder    # Changes made in ./folder will appear inside the path mentioned

### The Caddy Network
networks:
  default:
    external:
      name: caddy_net
	  
### Example .env variables in docker-compose.yml
${VARIABLE_A}

### Example variable in .env
VARIABLE_A=My_SQL_PASSWORD
```

## Common UFW commands
```
sudo ufw status numbered     #numbered list of all ufw rules
sudo delete 3                #delete rule #3 from ufw rules
sudo ufw enable
sudo ufw disable
sudo ufw allow 21/tcp        # Open TCP port 21
sudo ufw allow 21/udp        # Open UDP port 21
sudo ufw allow 21            # Open UDP & TCP for port 21.
```

## Caddy v2 References
To prevent automatic HTTPS on Caddy 2, append http:// to your CaddyFile Entries. Example Below.
```
http://subdomain.yourdomain.com {
    reverse_proxy wordpress:80
}
```

You can reverse proxy to IP Address + Port instead of by container name.
```
subdomain.yourdomain.com {
    reverse_proxy 192.168.16.5:8080
}
```

## Other Linux Commands
Check size of current directory
```
sudo du -sh ./
```
Check total disk usage
```
sudo df -h ./
```

## Random Rsync Notes For Container Migration
Remember to docker-compose down everything inside local machine so nothing shows up on docker container ls. 
```
# Generate keys. Choose defaults. Public key saves to ~/.ssh/id_rsa.pub
# Why do we do this? Because we set PermitRootLogin prohibit-password earlier.
# So we can only login root on the remote server via rsa key.

# As root user on local server generate Keys...
ssh-keygen -t rsa
cat ~/.ssh/id_rsa.pub

# Install Rsync. Local and remote server. 2 installs total.
sudo apt update
sudo apt install rsync

# Login to remote server
sudo su                 # to be the root user
mkdir ~/.ssh            # if it didn't exist already
cd .ssh
nano authorized_keys    # Append/Paste lines from id_rsa.pub generated from local server.
                        # Then save an exit.
						
# Back to your local server... The command
rsync -aP -e "ssh -i /home/<username>/.ssh/id_rsa" <source> <destination>

# Copy whole folder from source to destination example
rsync -aP -e "ssh -i /home/sammy/.ssh/id_rsa" ~/docker/someapp root@<IPADDRESS>:/home/sammy/ 

# Copy contents of folder from source to destination example # There is a / in front of ./docker in this case.
rsync -aP -e "ssh -i /home/sammy/.ssh/id_rsa" ~/docker/someapp/ root@<IPADDRESS>:/home/sammy/

# Rsync Flags
-a # The -a option is a combination flag. It stands for “archive” and syncs recursively and preserves symbolic links, special and device files, modification times, group, owner, and permissions
-e # specify ssh command...
-v # verbose
-P # progress + partial combined
-h # output numbers in human readable format
```
