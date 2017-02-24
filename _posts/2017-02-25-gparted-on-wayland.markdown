---
title: GParted на Wayland
layout: post
category: Wayland
tags:
- GParted
- Wayland
- Gnome
- fix
---

```
$ sudo gparted
#> No protocol specified

#> (gpartedbin:30127): Gtk-WARNING **: cannot open display: :0
```

Знакомо? Wayland уже хорош и пригоден к использованию, но иногда всё же вылазит всякая кака.

Чтобы запустить GParted под Wayland нужно разрешить ему(и всему локалхосту) цепляться к дисплею.

```
xhost +local:
```

И запускаем GParted как обычно.
