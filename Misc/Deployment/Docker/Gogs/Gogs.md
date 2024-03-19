---
tags:
  - docker
  - gogs
  - deployment
---
# Развертывание Gogs для работы без использования обратного прокси-сервера
```bash
docker volume create gogs-data
docker pull gogs/gogs:0.12.10
# docker pull dockerhub.net173.org/gogs/gogs:0.12.10
docker run --detach \
  --publish 8050:3000 \
  --publish 8051:22 \
  --restart=no \
  --volume gogs-data:/data \
  --name gogs \
  --network ptdemo-bridge \
  gogs/gogs:0.12.10
```
# Развертывание Gogs для работы с использованием обратного прокси-сервера
```bash
docker volume create gogs-data
docker pull gogs/gogs:0.12.10
# docker pull dockerhub.net173.org/gogs/gogs:0.12.10
docker run --detach \
  --restart=no \
  --volume gogs-data:/data \
  --name gogs \
  --network ptdemo-bridge \
  gogs/gogs:0.12.10
```