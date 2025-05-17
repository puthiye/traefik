
https://github.com/traefik/traefik/<br/>
https://doc.traefik.io/traefik/providers/docker/

```
services:
  traefik:
    image: traefik:v2.10
    container_name: traefik
    restart: unless-stopped
    command:
      - "--entrypoints.web.address=:80"
      - "--api.dashboard=true"
      - "--api.insecure=true"                  # Disable in production!
      - "--providers.docker=true"
      - "--providers.docker.exposedbydefault=false"
    ports:
      - "80:80"         # HTTP traffic
      - "8080:8080"     # Traefik dashboard
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock:ro"
    networks:
      - web

networks:
  web:
    external: true
```

## traefik docker compose
```
services:
  traefik:
    image: traefik:v2.10
    container_name: traefik
    restart: unless-stopped
    command:
      - "--entrypoints.web.address=:80"
      - "--api.dashboard=true"
      - "--providers.docker=true"
      - "--providers.docker.exposedbydefault=false"
    ports:
      - "80:80"         # HTTP traffic
      - "8080:8080"     # Traefik dashboard
```
Host Port 80 → Container Port 80
Traefik listens on port 80 inside the container (--entrypoints.web.address=:80).
This means Traefik will accept incoming HTTP traffic on port 80.

**NOTE::: No need to make any port changes on Traefik side, it remains as port 80.**

## adguard docker compose
```
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.adguardhome.rule=Host(`ad.home.lan`)"
      - "traefik.http.routers.adguardhome.entrypoints=web"
      - "traefik.http.services.adguardhome.loadbalancer.server.port=80"
```
adguardhome      - name of the container service
traefik.enable   - enables Traefik to manage this container.
rule=Host        - Traefik will route any request with the Host header matching adguard.home.lan to this container
server.port=80   - tells Traefik which port inside the container to forward requests to.

## Windows Host file

C:\Windows\System32\drivers\etc
```
192.168.10.129 home.lan
192.168.10.129 adguard.home.lan

C:\Users\unni_>ping home.lan

Pinging home.lan [192.168.10.129] with 32 bytes of data:
Reply from 192.168.10.129: bytes=32 time=5ms TTL=62
Reply from 192.168.10.129: bytes=32 time=7ms TTL=62
```

**NOTE:::::: restart both service using to pick up changes !!**
 docker compose down
 docker compose up -d

## How to access via traefik
Service - http://ad.home.lan/ <br/>
Traefik dashboard - http://home.lan:8080/      

## Phone 
Set your iPhone to use OpenWrt as DNS server:
```
Go to: Settings → Wi-Fi → (i icon) → Configure DNS
Choose Manual → Add Server: <IP of OpenWrt router>
```
In OpenWrt dnsmasq.conf   << MORE TO ADD HERE >>>>


## Troubleshooting

docker exec -it traefik sh
apk add --no-cache curl  # if curl not installed
curl http://adguardhome:80

//check adguard is listening on port 80
docker exec -it adguardhome netstat -tlnp

If not, switch from adguardhome.loadbalancer.server.port=80 to 3000






