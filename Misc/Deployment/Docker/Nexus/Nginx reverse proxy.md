---
tags:
- nginx
- reverseproxy
- nexus
---
## Nexus
``` nginx
server {
    listen                                 443 ssl;
    server_name                            nexus.ptdemo.local;
    server_tokens                          off;

    resolver                               127.0.0.11 valid=30s ipv6=off;

    access_log                             /var/log/nginx/nexus.log;
    error_log                              /var/log/nginx/nexus.log;

    ssl_certificate                        /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key                    /etc/nginx/ssl/nginx.key;

    location / {
        client_max_body_size               200M;
        proxy_connect_timeout              75;
        proxy_read_timeout                 600;
        proxy_send_timeout                 600;

        proxy_pass                         http://nexus:8081$request_uri;
        proxy_redirect                     off;
        proxy_set_header                   Host $http_host;
        proxy_set_header                   X-Real-IP $remote_addr;
        proxy_set_header                   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header                   X-Forwarded-Proto $scheme;
        proxy_set_header                   X-Forwarded-Protocol $scheme;
        proxy_set_header                   X-Url-Scheme $scheme;
    }
}

server {
    listen                                 443 ssl;
    server_name                            registry.ptdemo.local;
    server_tokens                          off;

    resolver                               127.0.0.11 valid=30s ipv6=off;

    access_log                             /var/log/nginx/registry.log;
    error_log                              /var/log/nginx/registry.log;

    ssl_certificate                        /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key                    /etc/nginx/ssl/nginx.key;

    location / {
        client_max_body_size               8G;
        proxy_buffering                    off;
        proxy_connect_timeout              75;
        proxy_read_timeout                 600;
        proxy_request_buffering            off;
        proxy_send_timeout                 600;

        proxy_pass                         http://nexus:8082$request_uri;
        proxy_redirect                     off;
        add_header                         X-Served-By $host;
        proxy_set_header                   Host $http_host;
        proxy_set_header                   X-Real-IP $remote_addr;
        proxy_set_header                   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header                   X-Forwarded-Proto $scheme;
        proxy_set_header                   X-Forwarded-Protocol $scheme;
        proxy_set_header                   X-Forwarded-Scheme $scheme;
        proxy_set_header                   X-Url-Scheme $scheme;
    }
}

server {
    listen                                 443 ssl;
    server_name                            dockerhub.ptdemo.local;
    server_tokens                          off;

    resolver                               127.0.0.11 valid=30s ipv6=off;

    access_log                             /var/log/nginx/dockerhub.log;
    error_log                              /var/log/nginx/dockerhub.log;

    ssl_certificate                        /etc/nginx/ssl/nginx.crt;
    ssl_certificate_key                    /etc/nginx/ssl/nginx.key;

    location / {
        client_max_body_size               8G;
        proxy_buffering                    off;
        proxy_connect_timeout              75;
        proxy_read_timeout                 600;
        proxy_request_buffering            off;
        proxy_send_timeout                 600;

        proxy_pass                         http://nexus:8083$request_uri;
        proxy_redirect                     off;
        add_header                         X-Served-By $host;
        proxy_set_header                   Host $http_host;
        proxy_set_header                   X-Real-IP $remote_addr;
        proxy_set_header                   X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header                   X-Forwarded-Proto $scheme;
        proxy_set_header                   X-Forwarded-Protocol $scheme;
        proxy_set_header                   X-Forwarded-Scheme $scheme;
        proxy_set_header                   X-Url-Scheme $scheme;
    }
}
```