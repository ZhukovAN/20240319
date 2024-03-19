---
tags:
  - docker
  - sonarqube
  - deployment
---
```bash
echo "vm.max_map_count = 524288" >> /etc/sysctl.conf
echo "fs.file-max = 131072" >> /etc/sysctl.conf
sysctl --system
docker volume create --name sonarqube-9-6-1-data
docker volume create --name sonarqube-9-6-1-logs
docker volume create --name sonarqube-9-6-1-extensions
docker run --detach --name sonarqube-9-6-1 \
  --publish 8150:9000 \
  --volume sonarqube-9-6-1-data:/opt/sonarqube/data \
  --volume sonarqube-9-6-1-extensions:/opt/sonarqube/extensions \
  --volume sonarqube-9-6-1-logs:/opt/sonarqube/logs \
  --stop-timeout 3600 sonarqube:9.6.1-community
```