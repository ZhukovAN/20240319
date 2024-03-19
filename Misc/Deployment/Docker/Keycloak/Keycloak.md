---
tags:
  - docker
  - keycloak
  - deployment
origin:
  - https://www.mastertheboss.com/keycloak/keycloak-with-docker/
  - https://stackoverflow.com/questions/69812281/how-to-save-the-keycloak-data-when-running-inside-docker-container
---
``` bash
# Параметр --proxy-edge обеспечивает возможность открытого HTTP-взаимодействия между обратным прокси-сервером и Keycloak
docker run --detach \
  --name keycloak \
  --network ptdemo-bridge \
  --volume keycloak-data:/opt/keycloak/data/ \
  --env KEYCLOAK_ADMIN=admin \
  --env KEYCLOAK_ADMIN_PASSWORD=P@ssw0rd \
  dockerhub.net173.org/keycloak/keycloak:17.0.1 \
  start-dev \
  --proxy=edge \
  --hostname-strict=false \
  --hostname-strict-https=false
```
``` bash
docker run --detach \
  --name keycloak \
  --network ptdemo-bridge \
  --volume keycloak-data:/opt/keycloak/data/ \
  --env KEYCLOAK_ADMIN=admin \
  --env KEYCLOAK_ADMIN_PASSWORD=P@ssw0rd \
  quay.io/keycloak/keycloak:17.0.1 \
  start-dev \
  --proxy=edge \
  --hostname-strict=false \
  --hostname-strict-https=false
```