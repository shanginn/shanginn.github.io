---
title: Запускаем Linux в браузере. Часть 2
layout: post
category: tutorial
tags:
- tutorial
- linux
- wasm
---

Сегодня поговорим о сборке образа через Docker и последующим запуском его в браузере.

- [Часть 1: Предисловие]({{site.basepath}}/linux-in-the-browser-part-1/)
- Часть 2: Запускаем Debian
- Чвсть 3: Настраиваем сеть

# Часть 2

## Сборка v86

Склонируем репу себе:

```bash
git clone git@github.com:copy/v86.git
cd v86
```

И сразу создадим папочку по образы:
```bash
mkdir images
```

Для сборки самого v86 в WASM нам потребуется как минимум:
- make
- Rust с возможностью компилировать в wasm32-unknown-unknown
- Версия clang совместимая с Rust
- nodejs (требуется свежая, у меня v18.2.0 работает)

Если у вас всё ещё не стоит Rust, [срочно исправляйте](https://www.rust-lang.org/ru/tools/install). Чтобы добавить возможность компилировать в wasm32-unknown-unknown выполните:

```bash
rustup target add wasm32-unknown-unknown
```

> *Если вы полезли запускать debian в браузере, с установкой всего остального не должно возникнуть проблем. Если это не так, пожалуйста, напишите мне, я дополню статью.*

Все подготовительные работы завершены, давайте собирать:
```bash
make
```

Если всё прошло хорошо, должен появиться `build/v86-debug.wasm`

```bash
ls -la build/v86-debug.wasm
# -rwxr-xr-x  1 user  group  15446948 17 июн 15:47 build/v86-debug.wasm
```

## Сборка образа Debian

Собирать образ будем через Docker, [установите его](https://docs.docker.com/engine/install/).

Идём в папочку с образом:

```bash
cd tools/docker/debian
```

При использовании ветки master у меня возникала ошибка

> WARNING: The requested image's platform (linux/386) does not match the detected host platform (linux/amd64) and no specific platform was requested

Если у вас появляется такая же (или похожая), исправить её очень просто. Нужно явно указать платформу **linux/386** при сборке в файле `build-container.sh`

```diff
# diff --git a/tools/docker/debian/build-container.sh b/tools/docker/debian/build-container.sh
-docker build . --rm --tag "$IMAGE_NAME"
+docker build . --platform linux/386 --rm --tag "$IMAGE_NAME"

-docker create -t -i --name "$CONTAINER_NAME" "$IMAGE_NAME" bash
+docker create --platform linux/386 -t -i --name "$CONTAINER_NAME" "$IMAGE_NAME" bash
```

А в остальном всё просто, запускаем:

```bash
./build-container.sh
```

Если всё прошло гладко, последнее сообщение после сборки будет таким:
```
./../../../images/debian-9p-rootfs.tar, ./../../../images/debian-9p-rootfs-flat and ./../../../images/debian-base-fs.json created.
```

Собственно, файл `images/debian-9p-rootfs.tar` и папка `images/debian-base-fs.json` должны существовать.

Всё, этого достаточно для запуска нашего образа в браузере!

## Стартуем

Нам потребуется добавить профиль с нашим образом и какой-нибудь веб сервер для отображения страницы. Начнём со второго.

Нам подойдёт любой вебсервер, готовый отдавать нам статику, я сейчас использую js'ный `serve`:

```bash
npm install -g serve
```

Теперь добавим профиль. Профили находятся в файле `src/browser/main.js` в переменной `oses`. Добавим в начало наш Debian:

```js
var oses = [
    {
        id: "debian",
        name: "Debian",
        memory_size: 512 * 1024 * 1024,
        vga_memory_size: 8 * 1024 * 1024,
        bzimage_initrd_from_filesystem: true,
        cmdline: "rw init=/bin/systemd root=host9p console=ttyS0 spectre_v2=off pti=off",
        filesystem: {
            "baseurl": host + "debian-9p-rootfs-flat/",
            "basefs": { url: host + "debian-base-fs.json" }
        }
    },
    {
        id: "archlinux",
    ...
```

Сохраняем, идёт обратно в корень репы и запускаем наш веб сервер:

```bash
serve
#   ┌────────────────────────────────────────┐
#   │                                        │
#   │   Serving!                             │
#   │                                        │
#   │   Local:  http://localhost:3000        │
#   │                                        │
#   │   Copied local address to clipboard!   │
#   │                                        │
#   └────────────────────────────────────────┘
```

Идём смотреть на debian по адресу http://localhost:3000/debug?profile=debian

Загрузка займёт пару минут, наберитесь терпения. Если всё пойдёт хорошо, процесс загрузки будет выглядеть как-то так:

![]({{ site.basepath }} /images/posts/linux-in-the-browser/debian-boot-1.png)
![]({{ site.basepath }} /images/posts/linux-in-the-browser/debian-boot-2.png)
![]({{ site.basepath }} /images/posts/linux-in-the-browser/debian-boot-3.png)

## Сохраняем состояние после загруки

Каждый раз ждать загрузку эту долго, можно один раз загрузиться локально и сохранить это состояние в файл, чтобы потом его загружаться из него. Файл будет немаленький, но его можно будет закешировать, так что профит очевиден.

Для начала нужно дособрать необходимые зависимости (для этого нужна будет установленная java):
```bash
make all
```

Должен появится файл `build/libv86.js`

Идём обратно в папку с образом debian'а и сохраняемся.

```bash
cd tools/docker/debian/
./build-state.js
```

И просто ждите. Последнее сообщение будет выглядеть примерно так:

> root@localhost:~# Saved as /user/v86/images/debian-state-base.bin

У меня охранённый state вышел на 130 метров, подъёмно:
```bash
ls -lah ../../../images/debian-state-base.bin
-rw-r--r--  1 user  group   130M 22 июн 22:56 ../../../images/debian-state-base.bin
```

Поравим немного наш профиль в `src/browser/main.js`:

```js
var oses = [
    {
        id: "debian",
        name: "Debian",
        memory_size: 512 * 1024 * 1024,
        vga_memory_size: 8 * 1024 * 1024,
        state: {"url": host + "debian-state-base.bin" },
        filesystem: {
            "baseurl": host + "debian-9p-rootfs-flat/",
            "basefs": { url: host + "debian-base-fs.json" }
        }
    },
    ...
```

И снова идите смотреть http://localhost:3000/debug?profile=debian

Готово! Теперь этапа загрузки не должно быть и вас сразу должна встретить консоль.

# Что дальше?

Самое очевидное - изменение образа в Dockerfile под свои нужны: ставьте и убирайте зависимости, или вообще используйте другие образы. Я, например, собрал себе [slim-версию последнего Debian](https://github.com/shanginn/v86-debian-image-build/blob/master/Dockerfile).

А в следующей части (которая выйдет кто знает когда) мы настроим интернет и будет полноценный (хоть и очень медленный) Linux в браузере!

До новых встреч!
