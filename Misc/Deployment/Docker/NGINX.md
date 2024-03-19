---
tags:
  - deployment
  - docker
  - nginx
---
``` bash
docker run --detach \
  --name nginx \
  --network ptdemo-bridge \
  --restart unless-stopped \
  --publish 80:80 \
  --publish 443:443 \
  --volume nginx-config:/etc/nginx \
  --volume nginx-logs:/var/log/nginx \
  nginx
# Create key / certificate store folder
docker exec nginx mkdir -p /etc/nginx/ssl
# Copy key / certificate from host to container
docker cp /root/x509/ptdemo.local/ptdemo-generic-server.pem.crt nginx:/etc/nginx/ssl/nginx.crt
docker cp /root/x509/ptdemo.local/ptdemo-generic-server.pem.key nginx:/etc/nginx/ssl/nginx.key
# Check key / certificate are copied
docker exec nginx ls -la /etc/nginx/ssl
```