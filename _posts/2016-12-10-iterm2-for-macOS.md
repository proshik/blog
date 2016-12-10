---
layout: post
title: Install iTerm2 for macOS with oh-my-zsh
tags: [ iterm2, oh-my-zsh ]
excerpt_separator: <!--more-->
---

![]({{ site.baseurl }}assets/2016-12-10-iterm2-for-macOS/iterm-title.png)

### Ликбез

iTerm2 is a replacement for Terminal and the successor to iTerm.

Oh-My-Zsh is an open source, community-driven framework for managing your ZSH configuration. It comes bundled with a ton of helpful functions, helpers, plugins, themes, and a few things that make you shout...

<!--more-->

### Инструксьен

Установка, добавление шрифтов. Установка темы oh-my-zsh.


#### Установка iTerm2

В стандартном terminal устанавливаем iTerm2 через:

```
$ brew cask install iterm2 
```

*iTerm2 готово.*

#### Установка Oh-My-Zsh

Переходим на [GitHub Oh-My-Zsh](https://github.com/robbyrussell/oh-my-zsh), выбираем там из раздела "Basic Installation" установку "via curl" или "via wget".
Копируем, например:

```
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

вставляем в установленном iTerm2.

*На этом установка "Oh-My-Zsh" завершена.*

#### Добавление шрифта

Топаем на [GitHub powerline/fonts](https://github.com/powerline/fonts), выбираем подходящий шрифт, например "Inconsolata", открываем директорию с ним, качаем "Inconsolata for Powerline.otf" (выбрать "View Raw" после открытия файла на GitHub.com).

Устанавливаем "Inconsolata for Powerline.otf" в системе.

Идем в:
 
```
iTerm2 -> Preferences(Cmd + ,) -> Profiles -> Text -> Change Font.
```

Выбираем нужный. *Шрифт установлен.*

#### Изменение темы для Oh-My-Zsh

Открываем в iTerm2 путь:

```
$ ~/.zshrc
```

находим ZSH_THEME="..." и заместо "..." вставляем имя темы из [GitHub Oh-My-Zsh Themes](https://github.com/robbyrussell/oh-my-zsh/wiki/Themes), например "agnoster". *Тема выбрана.*

*На этом все!*