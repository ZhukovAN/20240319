---
tags:
  - docker
  - portainer
  - deployment
---
```
docker volume create portainer-data
docker run --detach \
  --publish 9443:9443 \
  --name=portainer \
  --restart=always \
  --volume /var/run/docker.sock:/var/run/docker.sock \
  --volume portainer-data:/data \
  portainer/portainer-ce:latest
```
При необходимости можно [[Обновление Portainer|обновить]] Portainer до последней версии, при этом все настройки будут сохранены.