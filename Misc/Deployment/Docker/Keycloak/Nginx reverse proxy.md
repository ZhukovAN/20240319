---
tags:
- nginx
- jenkins
- reverseproxy
---
``` nginx
upstream local-keycloak {
    server keycloak:8080;
}

server {
    server_name localhost.local;
    listen 80;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    http2 on;
    server_name keycloak.ptdemo.local;

    resolver 127.0.0.11 valid=30s ipv6=off;

    location / {
        proxy_pass       http://local-keycloak;
        proxy_redirect   off;
        add_header       X-Served-By $host;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Scheme $scheme;
        proxy_set_header X-Forwarded-Proto  $scheme;
        proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP          $remote_addr;
        proxy_set_header X-Forwarded-Port   443;
    }

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;
    ssl_prefer_server_ciphers on;

    access_log /var/log/nginx/keycloak.access.log;
    error_log /var/log/nginx/keycloak.error.log;
}
```

``` nginx
server {
    listen 80;
    server_name keycloak.ptdemo.local;
    return 301 https://$host$request_uri;
}

server {
    listen 443 ssl;
    server_name keycloak.ptdemo.local;

    resolver 127.0.0.11 valid=30s ipv6=off;

    location / {
        proxy_pass       http://keycloak:8080$request_uri;
        proxy_redirect   off;
        add_header       X-Served-By $host;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Scheme $scheme;
        proxy_set_header X-Forwarded-Proto  $scheme;
        proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
        proxy_set_header X-Real-IP          $remote_addr;
        proxy_set_header X-Forwarded-Port   443;
    }

    ssl_certificate /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key /etc/nginx/ssl/nginx.key;
    ssl_prefer_server_ciphers on;

    access_log /var/log/nginx/keycloak.access.log;
    error_log /var/log/nginx/keycloak.error.log;
}
```