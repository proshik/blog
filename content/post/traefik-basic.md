+++
title = "Traefik. Основные понятия"
date = ""
categories = [
    "DevOps"
]
tags = [ 
    "traefik, docker"
]
draft = true
+++

Traefik – это reverse proxy с поддержкой Docker, который предоставляет встроенную панель мониторинга. 
В статье будет описано как запустить traefik и portainer, а так же развернуть сервер postgresql. И проксоирование и балансировка запросов для thirty part services.

В качестве примера будет описано развертывание простой инфрастуктуры для персональных side проектов.

## Конфигурация и запуск Traefik

Конфигурация описывается в файле `traefik.toml`:

```toml
defaultEntryPoints = ["http", "https"]
# traefik web
[web]
address = ":8080"
  [web.auth.basic]
  users = ["admin:$apr1$TnJ/d5bG$p3YVBAN0FhIKmmpoaqr8l/"]

# Entrypoints, http and https
[entryPoints]
  [entryPoints.http]
  address = ":80"
    [entryPoints.http.redirect]
      entryPoint = "https"
  [entryPoints.https]
  address = ":443"
    [entryPoints.https.tls]
# Enable ACME (Let's Encrypt)
[acme]
    email = "proxoraleksandrovich@gmail.com"
    storage = "/acme.json"
    entryPoint = "https"
    onDemand = false
    OnHostRule = true
    [acme.httpChallenge]
        entryPoint = "http"
[[acme.domains]]
    main = "demo.environment.proshik.ru"
[[acme.domains]]
    main = "nexus.environment.proshik.ru"

# Traefik log
[traefikLog]
  filePath = "log/traefik.log"

[accessLog]
    filePath = "log/access.log"

# thirty part services
[file]
[frontends]
  [frontends.website]
  backend = "website"
  [frontends.website.routes.website]
  rule = "Host:demo.project.proshik.ru"

[backends]
  [backends.website]
    [backends.website.servers.website]
    url = "http://167.99.218.4:8000"
```

Для запуска опишем `docker-compose.yml` файл:

```yaml
version: '3'
services:
  traefik:
    image: traefik
    command: --docker --file
    ports:
      - "80:80"
      - "443:443"
      - "8080:8080"
    restart: always
    labels:
      - "traefik.enabled=true"
      - "traefik.frontend.rule=Host:traefik.environment.proshik.ru"
      - "traefik.port=8080"
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
      - "./traefik.toml:/traefik.toml"
      - "./acme.json:/acme.json"
      - "./log/:/log/"
    networks:
      - default
  portainer:
    image: portainer/portainer
    ports:
      - "9000:9000"
    labels:
      - traefik.frontend.rule=Host:portainer.environment.proshik.ru
      - traefik.backend=portainer-ui
      - raefik.docker.network=default
      - traefik.port=9000
    volumes:
      - "/var/run/docker.sock:/var/run/docker.sock"
    networks:
      - default
```

Соответственно запускаем 

```yaml
$ docker-compose up -d
```

## Postgresql

```yaml
version: "3"
services:
  db:
    image: postgres:10.3
    ports:
      - "5432:5432"
    expose:
      - "5432"
    restart: always
    environment:
      - POSTGRES_PASSWORD=123456
    labels:
      - traefik.enable=true
      - traefik.backend=postgresql
      - traefik.frontend.rule=Host:postgresql.environment.proshik.ru
      - traefik.docker.network=default
      - traefik.port=5432
    volumes:
      - "/var/run/postgres/postgres.sock:/var/run/postgres/postgres.sock"
      - "./dbdata:/var/lib/postgresql/data"
    networks:
      - default
```

## Portainer

```yaml
version: "3"
services:
  portainer:
    image: portainer/portainer
      labels:
        - traefik.frontend.rule=Host:portainer.environment.proshik.ru
        - traefik.backend=portainer
        - raefik.docker.network=default
        - traefik.port=9000
      volumes:
          - "/var/run/docker.sock:/var/run/docker.sock"
      restart: unless-stopped
    networks:
      - default
```

## Nexus. Artefacts and images

Предварительно нужно создать директорию:

```bash
$ mkdir nexus-data
$ chmod 777 nexus-data
```

Создать `docker-compose.yml` файл:

```yaml
version: '3'
services:
  nexus:
    image: sonatype/nexus3:3.9.0
    ports:
      - "8081:8081"
    expose:
      - "8081"
    env_file:
      - nexus.env
    restart: always
    labels:
      - traefik.enable=true
      - traefik.backend=nexus
      - traefik.frontend.rule=Host:nexus.environment.proshik.ru
      - traefik.docker.network=default
      - traefik.port=8081
    volumes:
      - ./nexus-data:/nexus-data
    networks:
      - default
```

И запустить командой:

```bash
$ docker-compose up
```