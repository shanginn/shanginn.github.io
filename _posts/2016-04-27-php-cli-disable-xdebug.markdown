---
title: Отключаем xdebug в php-cli
layout: post
category: php
tags:
- PHP
- xdebug
---

xdebug штука полезная и нужная, но не везде. Например, когда запускаешь php из коммандной строки, xdebug только мешает. На это ругается Composer, а ещё если у вас в PHPStorm настроен Code Sniffer, то во время дебага этот самый сниффер ругается, что скоро кончится таймаут и он повесится.

Я это уже поправил, поэтому полностью показать ошибку не могу, но суть примерно такая:

~~~
Possible tool process hangup after 5 sec.
~~~

Давайте же это исправим!

# К коллайдеру!

Чтобы отключить xdebug только для коммандной строки, но оставить его для веба нам нужен отдельный php.ini
В [документации](https://secure.php.net/manual/ru/configuration.file.php) к файлу конфигурации написано, что php-cli сначала будет искать php-cli.ini и если такого нет, то будет использовать php.ini

Создадим php-cli.ini идентичным php.ini и сразу посмотрим, какой конфиг использует php-cli.

~~~ bash
cp /etc/php.ini /etc/php-cli.ini
php --ini
# > Loaded Configuration File: /etc/php-cli.ini
~~~

# Мухи отдельно, котлеты отдельно

Осталось настроить xdebug только для fpm.

У меня конфиг xdebug'a лежал в php.d/15-xdebug.ini; оттуда его нужно удалить, предварительно записав содержимое в php.ini, а после этого пезагрузить php-fpm.

~~~ bash
cat /etc/php.d/15-xdebug.ini >> /etc/php.ini
rm /etc/php.d/15-xdebug.ini
systemctl reload php-fpm
~~~

Вот и всё ребята!
