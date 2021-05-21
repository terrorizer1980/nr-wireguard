[![Build Status](http://drone.getais.cloud/api/badges/tomasliumparas/nr-wireguard/status.svg)](http://drone.getais.cloud/tomasliumparas/nr-wireguard)

## nr-wireguard

Work in progress. Messess with wireguard via Wireguard API


## Docker example

```yaml
version: '3.6'
networks:
  vpn-net:
    driver: overlay

services:

  nr-wireguard:
    image: ghcr.io/tomasliumparas/nr-wireguard:latest
    container_name: nr-wireguard
    deploy:
      replicas: 1
      restart_policy:
        condition: any
    network_mode: host
    environment:
      TS_ENV: "production"
      TS_LOG_LEVEL: "info"
      WIREGUARD_API_URL: "http://127.0.0.1:8080/api/v1.0"

  wg-gen-web:
    image: vx3r/wg-gen-web:latest
    container_name: wg-gen-web
    restart: unless-stopped
    network_mode: "host"
    environment:
    - WG_CONF_DIR=/data
    - WG_INTERFACE_NAME=wg0-sme.conf
    - SMTP_HOST=smtp.gmail.com
    - SMTP_PORT=587
    - smtp_username=no-reply@gmail.com
    - SMTP_PASSWORD=******************
    - SMTP_FROM=Wg Gen Web <no-reply@gmail.com>
    - OAUTH2_PROVIDER_NAME=fake
    - WG_STATS_API=http://172.17.0.1:8182
    volumes:
    - /etc/wireguard:/data
  wg-json-api:
    image: james/wg-api:latest
    container_name: wg-json-api
    restart: unless-stopped
    cap_add:
    - NET_ADMIN
    network_mode: "host"
    command: wg-api --device wg0-sme --listen 172.17.0.1:8182
```