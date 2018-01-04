---
layout: post
title: Начальная настройка узла с Ubuntu
tags: [ linux, ubuntu ]
excerpt_separator: <!--more-->
---

### Создание нового пользователя

Создаем пользователя с ником "proshik". При создании пользователя необходимо будет указать пароль.

```
adduser proshik
```

### Добавление пользователя в группу root

Для этого необходимо залогиниться под пользователем root и выполнить команду ниже. Пользователь proshik будет добавлен в группу root

```
usermod -aG sudo proshik
```

### Настройка файервола с помощью iptables

Фаервол, встроенный в ядро Linux, называется Netfilter, а iptables — утилита для управления этим фаерволом.

Для определения того, какие порты прослушиваются на машине используем netstat

```
sudo netstat -tuwpln
```

//todo

### Установка Docker

Инструкции по ссылкам
- [Get Docker CE for Ubuntu](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu)
- [Install Docker Compose](https://docs.docker.com/compose/install/)
- [Install Docker Machine](https://docs.docker.com/machine/install-machine/)
