+++
title = "Docker. Обзор Swarm cluster."
date = ""
categories = [
    "DevOps"
]
tags = [ 
    "docker"
]
draft = true
+++

С основными понятиями *Docker* можно ознакомиться в статье []()...

docker swarm init --advertise-addr 188.166.79.155

docker swarm join --token SWMTKN-1-5voaf31yxwbaknglks55t1nai379cw33oyaaquihia1rofweje-ebw1byy61936m5ez5h6gqe7bj 188.166.79.155:23770


## Docker secret

https://docs.docker.com/engine/swarm/secrets/#use-secrets-in-compose

$ openssl rand -base64 20 | docker secret create db_password -
$ openssl rand -base64 20 | docker secret create mysql_root_password -
$ docker secret ls

$ docker network create -d overlay mysql_private

$ docker service create \
     --name mysql \
     --replicas 1 \
     --network mysql_private \
     --mount type=volume,source=mydata,destination=/var/lib/mysql \
     --secret source=mysql_root_password,target=mysql_root_password \
     --secret source=mysql_password,target=mysql_password \
     -e MYSQL_ROOT_PASSWORD_FILE="/run/secrets/mysql_root_password" \
     -e MYSQL_PASSWORD_FILE="/run/secrets/mysql_password" \
     -e MYSQL_USER="wordpress" \
     -e MYSQL_DATABASE="wordpress" \
     mysql:latest
     
$ docker service ls

$ docker service create \
     --name wordpress \
     --replicas 1 \
     --network mysql_private \
     --publish published=30000,target=80 \
     --mount type=volume,source=wpdata,destination=/var/www/html \
     --secret source=mysql_password,target=wp_db_password,mode=0400 \
     -e WORDPRESS_DB_USER="wordpress" \
     -e WORDPRESS_DB_PASSWORD_FILE="/run/secrets/wp_db_password" \
     -e WORDPRESS_DB_HOST="mysql:3306" \
     -e WORDPRESS_DB_NAME="wordpress" \
     wordpress:latest

$ docker service ls

$ docker container exec $(docker ps --filter name=wordpress.1.st3diqun412itdlk6j1w5o9iy -q) cat /run/secrets/wp_db_password

or 

$ docker run -ti debian /bin/bash

### stack

```yaml
version: '3.1'

services:
   db:
     image: mysql:latest
     volumes:
       - db_data:/var/lib/mysql
     environment:
       MYSQL_ROOT_PASSWORD_FILE: /run/secrets/db_root_password
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD_FILE: /run/secrets/db_password
     secrets:
       - db_root_password
       - db_password

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     ports:
       - "8000:80"
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD_FILE: /run/secrets/db_password
     secrets:
       - db_password


secrets:
   db_password:
     external: true
   db_root_password:
     file: db_root_password.txt

volumes:
    db_data:
```


## Docker swarm 

```
$ docker swarm init

... join nodes

$ docker network create -d overlay traefik-net

$ docker stack deploy traefik -c stack.yml

if need
$ docker stack rm traefik

volume in DO
$ docker volume create --driver dostorage --name my-volume

```

DO volume: https://github.com/omallo/docker-volume-plugin-dostorage
```
sudo curl \
  -sSL \
  -o /usr/bin/docker-volume-plugin-dostorage \
  https://github.com/omallo/docker-volume-plugin-dostorage/releases/download/v0.4.0/docker-volume-plugin-dostorage_linux_amd64
```
