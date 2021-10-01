# Local Reverse Proxy

Create Local Reverse Proxy using Nginx (run in Docker Container).

## Get started

### 1. Install Docker

https://docs.docker.com/get-docker/

### 2. Clone repository

```
git clone local-reverse-proxy
```

### 3. Configuration

*File config/reverse-proxy.conf:*

You can change proxy list here

```
server {
    listen 80;
    listen [::]:80;

    access_log /var/log/nginx/reverse-access.log;
    error_log /var/log/nginx/reverse-error.log;

    # Proxy from http://localhost/api inside Container to address https://mydomain.dev/api
    location /api {
        proxy_pass https://mydomain.dev;
    }

    # Proxy from http://localhost inside Container to http://localhost:3000 of Host
    location / {
        proxy_pass http://host.docker.internal:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "Upgrade";
        proxy_set_header Host $host;
    }

    # Other proxies ...
}
```

*File docker-compose.yml:*

Change port running (default is 8080)

```
version: "3"

services:
  web:
    image: nginx:alpine
    volumes:
      - ./config/:/etc/nginx/conf.d/
    # Mapping port 80 of Container to port 8080 of Host
    ports:
      - "8080:80"

```

### 4. Run

Using Docker Compose:

```
cd local-reverse-proxy
docker-compose up
```

