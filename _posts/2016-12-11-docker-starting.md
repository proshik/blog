---
layout: post
title: Minimal info about Docker
tags: [ docker ]
excerpt_separator: <!--more-->
---

![]({{ site.baseurl }}assets/2016-12-11-docker-starting/docker-image.png)

### Ликбез

Docker — программное обеспечение для автоматизации развёртывания и управления приложениями в среде виртуализации на уровне операционной системы.

<!--more-->

Сборка:
docker build .

Слон в докере:
docker run -d --name some-postgres -e POSTGRES_PASSWORD=password -e POSTGRES_USER=postgres -p 5432:5432 ea3704caee3b

Слон в докере с сохранением даты на хост машине:
docker run -d --name some-postgres -e POSTGRES_PASSWORD=password -e POSTGRES_USER=postgres -p 5432:5432 -v /tmp/docker/volume/thinkclearly/postgresql:/var/lib/postgresql/data postgres

Запусть вложенного docker-compose файла:
docker-compose -f docker-compose.yml -f docker-compose.local.yml up -d --build

ELK:
docker run -d --name elasticsearch -v /tmp/docker/volume/elk/data:/usr/share/elasticsearch/data -p 9200:9200 elasticsearch
docker run -d --name kibana --link elasticsearch:elasticsearch -e ELASTICSEARCH_URL=http://elasticsearch:9200 -p 5601:5601 kibana
docker run -d --name logstash --link elasticsearch:elasticsearch -p 5000:5000 -v /tmp/docker/volume/elk/config/logstash:/config-dir logstash -f /config-dir/logstash.conf

Приложение через syslog для ELK:
docker run -it --rm --name nginx --log-driver=syslog --log-opt syslog-address=tcp://172.17.0.4:5000 -p 80:80 nginx
