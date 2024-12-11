### example.com
```nginx 
proxy_cache_path /tmp/cacheapi levels=1:2 keys_zone=microcacheapi:100m max_size=1g inactive=1d use_temp_path=off;
server {
    listen 443 ssl http2 default_server;
    listen [::]:443 ssl http2 default_server;
    server_name example.com;

    location /api/ {
        # Rate Limiting
        limit_req zone=reqlimit burst=20; # Max burst of request
        limit_req_status 460; # Status to send
        # Connections Limiting
        limit_conn connlimit 20; # Number ofdownloads per IP        
        
        # Bandwidth Limiting
        limit_rate 4096k; # Speed limit (here is on kb/s)

        # Micro caching
        proxy_cache microcacheapi;
        proxy_cache_valid 200 1s;
        proxy_cache_use_stale updating;
        proxy_cache_background_update on;
        proxy_cache_lock on;

        proxy_pass http://localhost:8080;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header X-NginX-Proxy true;

    }
    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    location ~* \.(jpg|jpeg|png|gif|ico)$ {
       expires 30d;
    }
    location ~* \.(css|js)$ {
       expires 7d;
    }

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot

    # Pagespeed Module
    pagespeed on;
    pagespeed FileCachePath /var/cache/ngx_pagespeed_cache;
    location ~ "\.pagespeed\.([a-z]\.)?[a-z]{2}\.[^.]{10}\.[^.]+" {
    add_header "" "";
    }
    location ~ "^/pagespeed_static/" { }
    location ~ "^/ngx_pagespeed_beacon$" { }
    pagespeed RewriteLevel PassThrough;
    pagespeed EnableCachePurge on;
    pagespeed PurgeMethod PURGE;
    pagespeed EnableFilters prioritize_critical_css;
}
server {
    listen 80;
    listen [::]:80;
    server_name example.com;
    return 301 https://$server_name$request_uri;
}
server {
    listen 80;
    listen [::]:80;
    listen 443 ssl http2;
    listen [::]:443 ssl http2;
    server_name www.example.com;
    return 301 https://example.com$request_uri;
    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
}
```

### nginx.conf 

```nginx
user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 768;
    # multi_accept on;
}

http {

      # Basic Settings
      sendfile on;
      tcp_nopush on;
      tcp_nodelay on;
      keepalive_timeout 65;
      types_hash_max_size 2048;
      server_tokens off;

      # server_names_hash_bucket_size 64;
      # server_name_in_redirect off;

      include /etc/nginx/mime.types;
      default_type application/octet-stream;

      # SSL Settings
      ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
      ssl_prefer_server_ciphers on;
      ssl_ciphers ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GC$

      # Optimize session cache
      ssl_session_cache shared:SSL:50m;
      ssl_session_timeout 1d;

      # Enable session tickets
      ssl_session_tickets off;

      # OCSP Stapling
      ssl_stapling on;
      ssl_stapling_verify on;
      resolver 8.8.8.8 8.8.4.4 208.67.222.222 208.67.220.220 valid=60s;
      resolver_timeout 2s;

      # security headers
      add_header X-Frame-Options "SAMEORIGIN" always;
      add_header X-XSS-Protection "1; mode=block" always;
      add_header X-Content-Type-Options "nosniff" always;
      add_header Referrer-Policy "no-referrer-when-downgrade" always;
      add_header Content-Security-Policy "script-src 'strict-dynamic' 'nonce-rAnd0m123' 'unsafe-inline' http: https:; object-src 'none'; base-uri 'none';" always;
      add_header Strict-Transport-Security "max-age=31536000; includeSubDomains; preload" always;
      add_header X-Cache-Status $upstream_cache_status;

      # Logging Settings
      access_log /var/log/nginx/access.log;
      error_log /var/log/nginx/error.log;

      # limits
      limit_req_log_level warn;
      limit_req_zone $binary_remote_addr zone=reqlimit:10m rate=10r/m;
      limit_conn_zone $binary_remote_addr zone=connlimit:100m;
      limit_conn servers 1000; # Simultaneous Connections

      include /etc/nginx/conf.d/*.conf;
      include /etc/nginx/sites-enabled/*;
}
```