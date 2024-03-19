---
tags:
  - docker
  - jenkins
  - deployment
---
# Развертывание сервера Jenkins для работы без использования обратного прокси-сервера
Дополнительно к штатной команде разворачивания Jenkins используется установка параметра запуска, определяющего размер кольцевого буфера журнала событий Jenkins. По умолчанию его значение равно 256, что зачастую недостаточно. Кроме того, в переменную окружения GIT_SSL_CAINFO передается путь к файлу, содержащему сертификаты доверенных УЦ, что обеспечивает возможность работы Git с репозиториями исходных кодов, использующими SSL на базе пользовательских сертификатов.
```bash
docker volume create jenkins-data
docker run --detach \
  --publish 8210:8080 \
  --publish 8211:50000 \
  --restart=unless-stopped \
  --volume jenkins-data:/var/jenkins_home \
  --name jenkins \
  --network ptdemo-bridge \
  --env JAVA_OPTS=-Dhudson.util.RingBufferLogHandler.defaultSize=65536 \
  --env GIT_SSL_CAINFO=/usr/local/share/ca-certificates/ca.crt \
  jenkins/jenkins:2.387.2
# Get initial configuration password
docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword
# Copy trusted CA certificate chain to container where GIT_SSL_CAINFO points to
cat /opt/ca/RootCA/certs/RootCA.pem.crt > ca.crt && cat /opt/ca/IntermediateCA/certs/IntermediateCA.pem.crt >> ca.crt
docker cp ca.crt jenkins-2-387-2:/usr/local/share/ca-certificates/
```
# Развертывание сервера Jenkins для работы с использованием обратного прокси-сервера
```bash
docker volume create jenkins-data
docker run --detach \
  --restart=unless-stopped \
  --volume jenkins-data:/var/jenkins_home \
  --name jenkins \
  --network ptdemo-bridge \
  --env JAVA_OPTS=-Dhudson.util.RingBufferLogHandler.defaultSize=65536 \
  --env GIT_SSL_CAINFO=/usr/local/share/ca-certificates/ca.crt \
  jenkins/jenkins:2.387.2
cat /opt/ca/RootCA/certs/RootCA.pem.crt > ca.crt && cat /opt/ca/IntermediateCA/certs/IntermediateCA.pem.crt >> ca.crt
docker cp ca.crt jenkins-2-387-2:/usr/local/share/ca-certificates/
docker exec jenkins update-ca-certificates
```
# Развертывание агента сборки Jenkins
```bash
docker run --detach \
  --init \
  --name jenkins-agent \
  --network ptdemo-bridge \
  jenkins/inbound-agent:jdk11 \
  -url http://jenkins:8080 \
  -workDir=/home/jenkins/agent \
  e2645783f661855fd5b6e466b0aaf026b422e071c02e5f1ee05d3d041504966e \
  ast-node
```