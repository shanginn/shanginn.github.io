---
title: Приложения с X11 под рутом
layout: post
category: help
tags:
- linux
- fix
---

~~~ bash
X Error: BadAccess (attempt to access private resource denied) 10
  Extension:    130 (MIT-SHM)
  Minor opcode: 1 (X_ShmAttach)
  Resource id:  0x162
X Error: BadShmSeg (invalid shared segment parameter) 128
  Extension:    130 (MIT-SHM)
  Minor opcode: 5 (X_ShmCreatePixmap)
  Resource id:  0xd6
X Error: BadDrawable (invalid Pixmap or Window parameter) 9
  Major opcode: 62 (X_CopyArea)
  Resource id:  0x2200011
X Error: BadDrawable (invalid Pixmap or Window parameter) 9
  Major opcode: 62 (X_CopyArea)
  Resource id:  0x2200011
~~~

Впервые такая ошибка возникла у меня при попытке запуска **Unetbootin**. Он требует рута для запуска.
Без рута интерфейс выглядит нормально, но сразу просит запустить себя под рутом.

А вот с рутом интерфейс выглядит никак: пустое окно и ошибки в терминале, которые вы видите сверху.

Справиться с этим очень просто: нужно зайти под рутом, добавить переменную среды и запустить приложение

~~~ bash
$ su
# export QT_X11_NO_MITSHM=1
# unetbootin
~~~

Готово!