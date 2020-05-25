---
title: Увеличиваем качество видео с помощью waifu2x на macos
layout: post
category: tutorial
tags:
- ffmpeg
- waifu2x
- macos
- bash
- video
---

Недавно я перевёл и озвучил [старый боян](https://www.youtube.com/watch?v=IGTr8iKWZJg)

Но оригинальное видео (датируется 2007)[https://www.imdb.com/title/tt10943642/] годом, так что качество оставляло желать лучшего, а я слышал, что нейронки умеют его улучшать.

Быстрый гуглёж по инструментам навёл меня на (waifu2x)[https://github.com/nagadomi/waifu2x], которая как раз позволяет улучшать качество картинок... только картинок, а не видео.

Для видео я нашел, например, (video2x)[https://github.com/k4yt3x/video2x]. Или, как утверждают авторы, более быстрая реализация (dandere2x)[https://github.com/aka-katto/dandere2x]. Но не было бы этой статьи если бы у меня получилось их скомпилировать под мак и всё было бы так просто.

**Может оно и просто, но у меня не получилось быстро, а долго я не пытался**

## Приступим

> Это ручная реализация в лоб из того что было, главное, чтобы работало.

Нам потребуется 3 вещи: waifu2x, ffmpeg и bash

К счастью, на момент написания этой статьи (macOS Catalina 10.15.4), всё это было в brew:

```bash
brew install imxieyi/waifu2x/waifu2x ffmpeg
```

Но для любителей, под macos у меня получилось собрать (c++ реализацию waifu2x)[https://github.com/tanakamura/waifu2x-converter-cpp] по (этому гайду)[https://www.reddit.com/r/waifu2x/comments/8whxl1/i_got_waifu2x_to_work_on_my_mac_heres_what_i_did/], но разницы с версией из brew лично я не ощутил (но у cpp версии параметров больше)

## Превращаем видео в картинки

```bash
mkdir frames
ffmpeg -i video.mp4 frames/frame-%d.png
```

## Улучшаем качество каждой картинки

Для удобства я создал пройтеший bash скриптец

```bash
vim v2x.sh && chmod +x v2x.sh
```

Туда вписываем пути.

```bash
#!/bin/bash

BASEPATH=$(PWD)
INPATH=$BASEPATH/frames
OUTPATH=$BASEPATH/x2frames

mkdir $OUTPATH

for f in $INPATH/*.png; do
  waifu2x -s 2 -n 0 -i $f -o $OUTPATH/$(basename -- $f) > /dev/null
done
```

> Можно убрать `> /dev/null`, если хотите видеть вывод
> И в зависимости от версии waifu2x параметры меняются, на это тоже стоит обратить внимание

Запускаем и ждём... процесс не быстрый.

```bash
./v2x.sh
```

Потом можно поменять INPATH на x2frames, а OUTPATH на x4frames и прогнать ещё разок, чтобы улучшить качество ещё разок

## Собираем обратно в видео

Для начала надо узнать fps оригинального видео. Для этого делаем `ffmpeg -i video.mp4` и ищем там fps

И дальше выполняем очень большую, но простую команду:

```bash
ffmpeg -r 29.97 -pattern_type sequence -i x2frames/frame-%d.png -i video.mp4 -map 0:v:0 -map 1:a:0 -vcodec libx264 -pix_fmt yuv420p scaled.mp4
```

Где
* -r - fps
* -pattern_type sequence -i x2frames/frame-%d.png - способ поиска кадров для видео
* -i video.mp4 - исходное видео для изъятия звука
* -map 0:v:0 - из *0* (первого) инпута (наши кадры) берём *v*(видеодорожку) под номером *0*(первую)
* -map 1:a:0 - значит из *1* (второго) инпута берём *a*(аудиодорожку) с индексом *0*(первую)
* -vcodec libx264 -pix_fmt yuv420p - кодек и формат пикселей, как на исходном видео
* scaled.mp4 - название выходного файла

## Вот и всё!

Решение в лоб, ручное, но оно работает. Можно всё это целиком обернуть в питоновский или баш скрипт, распараллелить, привлечь видеокарту... И многое-многое другое, но это всё многообразие и простор для творчества я оставляю вам!