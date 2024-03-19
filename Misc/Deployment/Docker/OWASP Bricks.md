---
tags:
- docker
- deployment
- owaspbricks
---
``` bash
docker run --detach \
  --publish 8080:80 \
  --restart=unless-stopped \
  --name owasp-bricks \
  --network ptdemo-bridge \
  citizenstig/owaspbricks
```
