---
title: GParted на Wayland. Чиним "cannot open display"
layout: post
category: help
tags:
- GParted
- Wayland
- Gnome
- fix
---

Wayland уже хорош и пригоден к использованию, но иногда всё же вылазит всякая кака.

```
$ sudo gparted
#> No protocol specified

#> (gpartedbin:30127): Gtk-WARNING **: cannot open display: :0
```

Знакомо?

Чтобы запустить GParted под Wayland нужно разрешить ему(и всему локалхосту) цепляться к дисплею.

```
xhost +local:
```

И запускаем GParted как обычно.
