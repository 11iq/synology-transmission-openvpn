# Synology (DSM 7.1.1-42962) Transmission + Openvpn setup in docker (MULLVAD VPN)

1. (optional) create new user for this container via synology dsm gui e.g.: docker-user (users group)
2. create new shared folder on synology e.g.: torrents (/volume1/torrents) and add read/write permission for docker-user
3. create transmission folder e.g.: /volume1/docker/transmission
4. create docker-compose.yaml inside the folder and paste content of docker-compose.yaml from this repo
5. change following variables:
```
OPENVPN_PROVIDER    (your vpn provider https://haugene.github.io/docker-transmission-openvpn/supported-providers/)
OPENVPN_CONFIG    (choose one from https://github.com/haugene/vpn-configs-contrib/tree/main/openvpn based of your vpn provider)
OPENVPN_USERNAME=XXXX
OPENVPN_PASSWORD=XXXX
LOCAL_NETWORK=XXXX/XX
PGID=XXXX     (docker-user group id)
PUID=XXXX     (docker-user user id)
```
keep the following lines if you're using mullvad, otherwise remove them
```
        sysctls:
            - "net.ipv6.conf.all.disable_ipv6=0"
```
[https://haugene.github.io/docker-transmission-openvpn/provider-specific/]

6. run from /volume1/docker/transmission folder
```
docker-compose up
```
7. check newly created network inside docker -> networks and add firewall rule for docker subnet. Also make sure, you add firewall rule for 9091 for your control host.
8. stop the docker-compose
9. start as daemon
```
docker-compose up -d
```
10. check ip from inside the container: 
  docker exec -it <container ID> /bin/sh 
  curl ifconfig.me
11. open transmission webui
```
http://<LOCAL_NETWORK>:9091
```

#### Note:
Newly downloaded torrent files in /volume1/torrents/watch will be automatically added to transmission and started.

Not finished torrents are stored in /volume1/torrents/incomplete

If you want to download movies to different folder, upload the torrent manually and set destination path to e.g.: /data/completed/Movies

Mullvad users must have peer listening port used in transmission (settings -> network -> port [check status of port]) fowarded in mullvad account (account management -> port forwarding -> choose city you used in OPENVPN_CONFIG + No device (only OpenVPN)) to be able to seed.
