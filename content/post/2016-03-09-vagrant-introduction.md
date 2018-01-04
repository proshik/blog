+++
title = "Vagrant. Introduction. Part 1"
date = "2016-03-09"
categories = [
    "DevOps"
]
tags = [ 
    "vagrant"
]
thumbnail = "/media/2016-03-10-vagrant-introduction/thumbnail.png"
+++

Vagrant - это такая утилита, позволяющая быстро и легко создавать и запускать виртуалки через CLI. 
Мы будем использовать в качестве *провайдера* VirtualBox, но для этой цели так-же подходит VMWare, LXC и.др.
Для чего это может быть нужно:

<!--more-->

* быстро запустить виртуалку, чтобы что-то проверить;
* тестирование/отладка приложений;
* необходима OS, отличная от рабочей;
* ну и так далее.


### Установка Vagrant

#### Для _OS X_ 
Удобно поставить все необхдоимое через *brew cask*:

Для управления виртуалками понадобится VirtualBox.

```
$ brew cask install virtualbox 
```

Сам Vagrant.

```
$ brew cask install vagrant
```

Еще можно поставить менеджер виртуалок [Vagrant Manager](http://vagrantmanager.com). Тому кто часто пользуется возможно будет удобно.

```
$ brew cask install vagrant-manager
```

####  Для _Linux_ 
Cкачать [VirtualBox](https://www.virtualbox.org/wiki/Linux_Downloads), [Vagrant](https://www.vagrantup.com/downloads.html) и установить.

```
$ sudo dpkg -i virtualbox-5.1. ... .deb
$ sudo dpkg -i vagrant-1.8.1. ... .deb
```

####  Для _Window_ 
Cкачать [VirtualBox](https://www.virtualbox.org/wiki/Downloads) и [Vagrant](https://www.vagrantup.com/downloads.html).

### Использование Vagrant

Вместо использования образов в Vagrant введено понятие боксов - "box", это такой компактный образ системы, из которых можно создавать новые виртуалки. [Здесь](https://atlas.hashicorp.com/boxes/search) каталог образов, которые можно скачать и использовать.

Скачаем образ с Ubuntu 14.04 и откроем список всех доступных локально образов.

```
vagrant box add ubuntu/trusty64
vagrant box list
```

Теперь где-нибудь создадим виртуалку из образа.

```
mkdir ~/user/vagrant/trusty
cd ~/user/vagrant/trusty
vagrant init ubuntu/trusty64
```

Все, готово, без всякого ползания по UI курсором и выбора кучи параметров ВМ.

В текущей директории будет создан файл с именем "Vagrantfile", который описывает конфигурацию виртуальной машины. Это и ограничиение по используемым ресурсам, настройка сети, проброс портов и т.д.

К примеру для создание фиксированного IP и проброса порта для ВМ надо раскомментировать строчки:

```
config.vm.network "private_network", ip: "192.168.2.1"
config.vm.network "forwarded_port", guest: 80, host: 8081
```

Для запуска виртуальной машины надо всего лишь выполнить в каталоге где был выполнен *init*:

```
vagrant up
```

Зайти по ssh в эту виртуалку можно с помощью следующей команды:

```
vagrant ssh
```

Так можно запускать сколь угодно много виртуалок (на самом деле не знаю сколько, но вероятно много) и отслеживать их состояние через:

```
vagrant global-status
```

Бывают косяки и виртулака не удаляется из *global-status*, тогда поможет:

```
vagrant global-status --prune
```

Виртуалки можно ставить на паузу и запускать после посредством id полученного из *global-status*:

```
vagrant suspend 5f4d
vagrant resume 5f4d
```

Остановка и запуск виртуалки:

```
vagrant halt 5f4d
vagrant up 5f4d
```

Удаление:

```
vagrant destroy 5f4d
```

#### Создание боксов из ВМ

В каталоге с "Vagrantfile" создать бокс:

```
vagrant package
```

Будет создан упакованный образ "package.box". Далее его необходимо так-же через "add" добавить и можно создавать на его основа ВМ:

```
vagrant box add custom_trusty64 package.box
vagrant box list
vagrant box remove custom_trusty64
```

*Пока все!*
