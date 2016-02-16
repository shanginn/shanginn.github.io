---
title: Can I use из командной строки
layout: post
category: CSS
tags:
- CSS
- Can I use
- CLI
---

Всем привет! Сегодня будет короткий пост про интерфейс к [Can I use](http://caniuse.com/) для командной строки.  
Да, я люблю пользоваться терминалом :)

## Что за зверь такой?

`Can I use` - это очень полезный сайт; на нём можно посмотреть, поддерживается ли какая-нибудь фича во всех браузерах. Я его использую, чтобы вспомнить в каких браузерах поддерживается то или иное CSS-свойство.

## Установка

Интерфейс для коммандной строки найден в [этом блоге](https://samgentle.com/posts/2015-03-15-caniuse-for-the-command-line). Документацию можно найти на [гитхабе](https://github.com/sgentle/caniuse-cmd).  
Реализован он в виде модуля для `nodejs`, поэтому ставить его будем через `npm`

~~~ bash
sudo npm install -g caniuse-cmd
~~~

Ждём, пока npm вытянет все зависимости и всё - можно пользовать.

## Использование 

### Базавое использование
~~~ bash
caniuse *СВОЙСТВО*
~~~

Пример:
![]({{ site.basepath }} /images/posts/caniuse-cli.png)

### Опции
~~~ bash
caniuse --help
~~~

~~~
Options:
  --short, -s            Short output: show browsers on one line and don't
                         display notes or description (default when displaying
                         multiple results)                             [boolean]
  --long, -l             Long output: show more information (default when
                         displaying a single result)                   [boolean]
  --oneline, -1          One-line output: just global percentages, no per-
                         browser info                 [boolean] [default: false]
  --oneline-browser, -2  One-line output with browser info, implies --abbrev and
                         --current                    [boolean] [default: false]
  --abbrev, -a           Abbreviate browser names     [boolean] [default: false]
  --percentages, -p      Include browser version usage percentages
                                                      [boolean] [default: false]
  --future, -f           Include future browser versions
                                                      [boolean] [default: false]
  --current, -c          Don't include old browser versions, equivalent to --era
                         e0                           [boolean] [default: false]
  --era, -e              How many versions back to go, e0 to e-44       [string]
  --mobile, -m           Include mobile browsers      [boolean] [default: false]
  --desktop, -d          Include desktop browsers      [boolean] [default: true]
  --browser, -b          Show results for these browsers, comma-separated (ie,
                         edge,firefox,chrome,safari,opera,ios_saf,op_mini,
                         android,bb,op_mob,and_chr,and_ff,ie_mob,and_uc)
                                                                        [string]
  --ascii, -A            UTF-8 symbols replacement with ASCII description
                                                      [boolean] [default: false]
  --web, -w              Go to the search page on caniuse.com
                                                      [boolean] [default: false]
  --config, -C           Path to JSON config file
                                [string] [default: "/home/shance/.caniuse.json"]
  --help                 Show help                                     [boolean]
~~~

Я думаю, понятно и без перевода.

### Конфиг
Опций многовато, поэтому можно написать свой конфиг файл.  
Например, если вы планируете разработку под мобильники и хотите поддерживать 2 последнии версии, то создайте файл `.caniuse.json` в своей домашней директории и напишите туда следующее:

~~~ json
{
  "era": "e-2",
  "mobile": true,
  "desktop": false
}
~~~

Вот и всё на сегодня.