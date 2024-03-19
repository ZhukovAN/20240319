---
tags:
- nginx
- reverseproxy
- gogs
---
``` nginx
server {
    listen 80;
    server_name gogs.ptdemo.local;

    location / {
        return 301 https://gogs.ptdemo.local$request_uri;
    }
}

server {
    listen 443 ssl;
    http2 on;
    server_name gogs.ptdemo.local;

    resolver 127.0.0.11 valid=30s ipv6=off;

    # allow large uploads of files - refer to nginx documentation
    client_max_body_size 8G;

    location / {
        proxy_pass       http://gogs:3000$request_uri;
        add_header       X-Served-By $host;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Scheme $scheme;
        proxy_set_header X-Forwarded-Proto  $scheme;
        proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP          $remote_addr;
    }

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;
    ssl_prefer_server_ciphers on;

    access_log /var/log/nginx/gogs.access.log;
    error_log /var/log/nginx/gogs.error.log;
}

```