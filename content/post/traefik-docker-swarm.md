+++
title = "Traefik. Деплой Docker swarm cluster"
date = ""
categories = [
    "DevOps"
]
tags = [ 
    "traefik, docker"
]
draft = true
+++

docker stack deploy traefik -c docker-compose.yml
docker stack rm traefik

docker stack deploy postgresql -c docker-compose.yml
docker stack rm postgresql

```yaml
version: "3.3"
services:
  traefik:
    image: traefik
    command: --web --docker --docker.swarmmode --docker.watch --docker.domain=docker.localhost --logLevel=DEBUG
    ports:
      - 80:80
      - 8080:8080
      - 443:443
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./traefik.toml:/traefik.toml
      - ./acme.json:/acme.json
      - ./log/:/log/     
    networks:
      - traefik-net
    deploy:
      placement:
        constraints: [node.role==manager]
      restart_policy:
        condition: on-failure
      labels:
        - traefik.port=8080
        - traefik.backend=traefik
        - traefik.docker.network=traefik_traefik-net
        - traefik.frontend.rule=Host:traf.environment.proshik.ru

  web:
    image: emilevauge/whoami
    networks:
      - traefik-net
    deploy:
      replicas: 2
      labels:
        - traefik.port=80
        - traefik.docker.network=traefik_traefik-net
        - traefik.frontend.rule=Host:web.environment.proshik.ru

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - 5000:8080
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - traefik-net
    deploy:
      placement:
        constraints: [node.role==manager]
      labels:
        - traefik.port=5000
        - traefik.backend=visualizer
        - traefik.docker.network=traefik_traefik-net
        - traefik.frontend.rule=Host:viz.environment.proshik.ru

  portainer:
    image: portainer/portainer
    networks:
      - traefik-net
    ports:
      - 9000:9000
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./portainer/data:/data
    deploy:
      placement:
        constraints: [node.role==manager]
      labels:
        - traefik.port=9000
        - traefik.backend=portainer-ui
        - traefik.docker.network=traefik_traefik-net
        - traefik.frontend.rule=Host:pr.environment.proshik.ru

networks:
  traefik-net:
    driver: overlay
```

```yaml
version: "3.3"
services:
  traefik:
    image: traefik
    command: --web --docker --docker.swarmmode --logLevel=DEBUG
    ports:
      - 80:80
      - 8080:8080
      - 443:443
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./traefik.toml:/traefik.toml
      - ./acme.json:/acme.json
      - ./log/:/log/     
    networks:
      - traefik-net
    deploy:
      placement:
        constraints: [node.role==manager]
      restart_policy:
        condition: on-failure
      labels:
        - traefik.port=8080
        - traefik.enable=true
        - traefik.docker.network=traefik_traefik-net
        - traefik.frontend.rule=Host:traf.environment.proshik.ru

  web:
    image: emilevauge/whoami
    networks:
      - traefik-net
    deploy:
      replicas: 2
      labels:
        - traefik.port=80
        - traefik.docker.network=traefik_traefik-net
        - traefik.frontend.rule=Host:web.environment.proshik.ru

  visualizer:
    image: dockersamples/visualizer:stable
    ports:
      - 5000:8080
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    networks:
      - traefik-net
    deploy:
      placement:
        constraints: [node.role==manager]
      labels:
        - traefik.port=5000
        - traefik.enable=true
        - traefik.backend=visualizer
        - traefik.docker.network=traefik_traefik-net
        - traefik.frontend.rule=Host:viz.environment.proshik.ru

  portainer:
    image: portainer/portainer
    networks:
      - traefik-net
    ports:
      - 9000:9000
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./portainer/data:/data
    deploy:
      placement:
        constraints: [node.role==manager]
      labels:
        - traefik.port=9000
        - traefik.enable=true
        - traefik.backend=portainer-ui
        - traefik.docker.network=traefik_traefik-net
        - traefik.frontend.rule=Host:pr.environment.proshik.ru

networks:
  traefik-net:
    driver: overlay
```

```yaml
version: "3.3"
services:
  db:
    image: postgres:9.6
    ports:
      - "5432:5432"
    networks:
      - traefik-net
    environment:
      - POSTGRES_PASSWORD=123456
    volumes:
      - ./dbdata:/var/lib/postgresql/data
    deploy:
      placement:
        constraints: [node.role==manager]
      restart_policy:
        condition: on-failure
      labels:
        - traefik.port=5432
        - traefik.enable=true
        - traefik.backend=postgresql
        - traefik.docker.network=postgresql_traefik-net
        - traefik.frontend.rule=Host:postgresql.environment.proshik.ru

networks:
  traefik-net:
    driver: overlay
```