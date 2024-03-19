---
tags:
  - docker
  - nexus
  - deployment
---
# Использование локального хранилища артефактов
``` bash
docker volume create nexus-data
docker run --detach \
  --publish 8081-8083:8081-8083 \
  --name nexus \
  --network ptdemo-bridge \
  --volume nexus-data:/nexus-data \
  sonatype/nexus3
docker exec -it nexus cat /nexus-data/admin.password
```
# Использование NFS для хранения артефактов Nexus
``` bash
docker volume create \
  --driver local \
  --opt type=nfs \
  --opt o=addr=192.168.0.4,vers=4,soft \
  --opt device=:/storage/data/mirror/nexus \
  nexus-data-nfs
docker run --detach \
  --publish 8081-8083:8081-8083 \
  --name nexus \
  --network domain-bridge \
  --volume nexus-data-nfs:/nexus-data \
  sonatype/nexus3
docker exec -it nexus cat /nexus-data/admin.password
```