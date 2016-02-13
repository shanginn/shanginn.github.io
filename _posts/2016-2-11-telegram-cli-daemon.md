---
layout: post
title: Telegram CLI - демон для systemd систем
category: Telegram CLI
tags:
- Telegram CLI
- systemd
---

В этом посте я расскажу, как написать конфиг файл для systemd, чтобы наш telegram-cli запускался вместе с системой и ждал наших команд.
## В чем проблема?
Вот [здесь](https://github.com/vysheng/tg/wiki/Running-Telegram-CLI-as-Daemon), например, описано, как сделать такого демона под debian-подобные системы, но у у меня на федоре такой трюк не пройдёт. Дело в том, что Fedora начиная с 15 версии использует **systemd** вместо **initd**. И в нём, **systemd**, не нужно писать полный скрипт для запуска демона со свичем, а нужно написать конфиг файл.  
Без долгих вступлений этим и займемся.

## telegram.service
Создаём и открываем в вашем любимом редакторе файл **telegram.service** по адресу **/etc/systemd/user/**

``` bash
sudo vim /etc/systemd/user/telegram.service
```
Сначала покажу его содержимое, а потом опишу, что к чему.

### Конфиг
``` ini
[Unit]
Description=Telegram CLI daemon

Wants=network.target
After=network.target

[Install]
WantedBy=default.target

[Service]
Type=simple
ExecStart=/opt/tg/bin/telegram-cli --wait-dialog-list -vvvv --disable-auto-accept --disable-readline --disable-colors --rsa-key=/opt/tg/tg-server.pub --logname=/var/log/telegramd.log --tcp-port=7313 --daemonize

KillSignal=SIGKILL
KillMode=process

Restart=on-failure
```

В принципе, всё и так понятно. Ждём пока загрузится сеть и запускаем **telegram-cli** с аргументами.  
**KillSignal=SIGKILL** установлено потому что по каким-то причинам **telegram-cli** достаточно вяло реагирует на **SIGTERM**, поэтому будем его просто глушить.  
Если вы устанавливали **telegram-cli** по [этой]({{ site.basepath }} /telegram-cli/) инструкции, то пути в порядке. Если же нет, то поменяйте их на ваши.  
Аргументы при запуске тоже говорят сами за себя, но давайте по ним пробежимся:

* **--wait-dialog-list** - подождать пока загрузится список диалогов
* **-vvvv** - выдавать максимум информации
* **--disable-auto-accept** - не принимать секретные чаты автоматически
* **--disable-readline** - выключить режим readline
* **--disable-colors** - отключить цвета
* **--rsa-key=/opt/tg/tg-server.pub** - публичный ключ сервера
* **--logname=/var/log/telegramd.log** - место для логов
* **--tcp-port=7313** - порт на котором думон будет слушать
* **--daemonize** - собственно, демонезация

### Стартуем
``` bash
systemctl --user start telegram
systemctl --user status telegram
```
Если вы видите похожую картину,
![]({{ site.basepath }} /images/tcli/tcli-daemon.png)
то добавляйте сервис в автозапуск.

``` bash
systemctl --user enable telegram
```
Теперь мы можем обращаться к этому демону через **netcat** таким образом:

``` bash
nc localhost 7313
```
И получаем ту же командную строку, но в ней нет автодополнения.  
Либо можно посылать аргументы через конвеер:

``` bash
echo "command" | nc localhost 7313
```
Не очень удобно, правда? В [следующей статье]({{ site.basepath }} /telegram-cli-fun/) мы напишем оболочку для этого дела, а ещё позже я расскажу как сделать автодополнение по *tab*'у под **zsh**.  
Stay tuned!