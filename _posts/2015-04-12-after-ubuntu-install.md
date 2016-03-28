---
layout: post
title: После установки Ubuntu
tags: [ linux, ubuntu ]
excerpt_separator: <!--more-->
---

### Вступление

В очередной раз вернувшись на малую родину, с уверенностью, что будет мало свободного времени, а
проверить почту, почитать новости мне хватит и планшета, естественно оказался не прав.
Решил, что форточка под номером 7, соверешнно не то, взял лишний жесткий и поставил на него 
Ubuntu. Только вот оказалось я ежедневно пользуюсь таким большим количеством программ,
что пришлось вспоминать как же все это устанавливать. Я вообще удивляюсь, как люди запоминают
название всех этих зависимостей и команд linux.

Поэтому вот памятка, что надо поставить после наката дистрибутива, для себя и людей, кому это может быть полезно.

<!--more-->

### В первую очередь
#### Google Chrome ####
```bash
wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add -
sudo sh -c 'echo "deb http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google-chrome.list'
sudo apt-get update
sudo apt-get install google-chrome-stable
```

#### Ubuntu Tweak ####
```bash
sudo add-apt-repository ppa:tualatrix/ppa
sudo apt-get update 
sudo apt-get install ubuntu-tweak
```

#### Skype ####
Добавляем репозиторий "партнеров Canonical"

```bash
sudo add-apt-repository "deb http://archive.canonical.com/ $(lsb_release -sc) partner"
```

Ищем в главном меню Dash "Программы и обновления", либо идем в Параметры системы -  Программы и обновления.
На вкладке "Другое ПО" ставим галку "Партнеры Canonical"
А теперь непосредственно ставим skype:

```bash
sudo apt-get update
sudo apt-get install skype && sudo apt-get -f install
```

#### Dropbox ####

```bash
cd ~ && wget -O - "https://www.dropbox.com/download?plat=lnx.x86_64" | tar xzf -
~/.dropbox-dist/dropboxd
```
#### SMPlayer ####
Иногда хочется посмотреть ~~скаченный с торрентов~~ купленный легально фильм

```bash
sudo add-apt-repository ppa:rvm/smplayer
sudo apt-get update
sudo apt-get install smplayer smplayer-themes smplayer-translations
```

#### 1Password ####
Понадобится еще Wine

```bash
sudo apt-get install wine
```

и сам 1Password, качаем [здесь](https://agilebits.com/onepassword/windows).

Затем устанавливаем 1Password

```bash
wine 1Password-4.1.0.530.exe
```

### Если зачется открыть IntelliJ IDEA ###
#### Java ####
```bash
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
```

##### Для java 7: #####
```bash
sudo apt-get install oracle-java7-installer
```
##### Для java 8: ######
```bash
sudo apt-get install oracle-java8-installer
```

Чтобы иметь несколько версий java в системе, можно выбирать используемую по умолчанию:

```bash
sudo update-java-alternatives -s java-7-oracle
```

Для других версий аналогично, просто меняем цифру версии.

#### Postgresql ####
В своих проектах я использую чаще всего именно эту СУБД.
Установка;

```bash
sudo apt-add-repository ppa:pitti/postgresql
sudo apt-get update
sudo apt-get install postgresql-9.4
```

Подключаемся к серверу postgresql и добавляем роль:

```sql
postgres=# CREATE DATABASE test_database;
postgres=# CREATE USER postgres WITH password '1';
```

#### Git, maven, gradle, geany ####
```bash
sudo apt-get install git
sudo apt-get install maven

sudo add-apt-repository ppa:cwchien/gradle
sudo apt-get update

sudo apt-get install gradle
sudo apt-get install geany
```


### Написать статью в блог a.k.a. dependency for Jekyll
Для работы jekyll понадобится ruby, и не какая то версия а именно 2.2.1, 
как указано в зависимостях на [GitHub Pages](https://pages.github.com/versions/).
Правда потом выяснится, что для работы jekyll нужна среда выполнения javascript, поэтому
предварительно поставим еще NodeJS и npm.

#### NodeJS ####
```
sudo apt-get install nodejs
sudo apt-get install npm
```
#### Ruby ####
```bash
sudo apt-get install curl zlib1g-dev build-essential libssl-dev libreadline-dev libyaml-dev libsqlite3-dev sqlite3 libxml2-dev libxslt1-dev libcurl4-openssl-dev python-software-properties libffi-dev

cd /tmp
wget http://ftp.ruby-lang.org/pub/ruby/2.2/ruby-2.2.1.tar.gz
tar -xzvf ruby-2.2.1.tar.gz
cd ruby-2.2.1/
./configure --prefix=/usr/local
make
make install
ruby -v
```

#### Менеджер пакетов Ruby ####
```bash
gem install bundler
```

#### Окружение Jekyll ####
За место установки необходимых для GitPages зависимостей, можно одним пакетом все доставить

```bash 
gem install github-pages
```
И теперь заходим в папку проекта и на http://localhost:4000 поднимаем сайт командой

```bash
jekyll serve
```

*Пока все! Т.к. писал уже после всех действий, вероятно часть забыл, 
но если что придет на ум - допишу!*