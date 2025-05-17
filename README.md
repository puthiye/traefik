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
      - "traefik.http.routers.adguardhome.rule=Host(`adguard.home.lan`)"
      - "traefik.http.routers.adguardhome.entrypoints=web"
      - "traefik.http.services.adguardhome.loadbalancer.server.port=80"
```
adguardhome      - name of the container service
traefik.enable   - enables Traefik to manage this container.
rule=Host        - Traefik will route any request with the Host header matching adguard.home.lan to this container
server.port=80   - tells Traefik which port inside the container to forward requests to.
