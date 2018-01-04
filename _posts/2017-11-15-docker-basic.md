+++
layout: post
title: Docker. Основные команды.
tags: [ docker ]
excerpt_separator: <!--more-->
---

docker build --rm -f Dockerfile -t 'translator:latest' . 

docker run --rm -p 8080:8080 'translator' 

docker rm $(docker ps -a -q)

docker rmi $(docker images -q -a)

docker exec -it heuristic_mccarthy /bin/bash
