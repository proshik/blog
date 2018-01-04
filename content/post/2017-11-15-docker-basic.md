+++
title = "Docker. Basic commands."
date = "2018-01-03"
categories = [
    "DevOps"
]
tags = [ 
    "docker"
]
draft = true
+++

docker build --rm -f Dockerfile -t 'translator:latest' . 

docker run --rm -p 8080:8080 'translator' 

docker rm $(docker ps -a -q)

docker rmi $(docker images -q -a)

docker exec -it heuristic_mccarthy /bin/bash
