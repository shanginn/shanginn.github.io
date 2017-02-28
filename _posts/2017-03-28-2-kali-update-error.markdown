---
title: failed to create file via template '/data/local/tmp/postgresql-common.XXXXXX'
layout: post
category: help
tags:
- Kali Linux
- dpkg
- fix
---

После установки [Kali Linux на Nexus 5]({{ site.basepath }}/nethunter-linux-root-toolkit/) естественным желанием было его обновить. Но что-то пошло совсем не так.

```
Setting up postgresql-common (179) ...
supported-versions: WARNING! Unknown distribution: kali
debian found in ID_LIKE, treating as Debian
supported-versions: WARNING: Unknown Debian release: 2016.2
mktemp: failed to create file via template '/data/local/tmp/postgresql-common.XXXXXX': No such file or directory
dpkg: error processing package postgresql-common (--configure):
 subprocess installed post-installation script returned error exit status 1
Setting up ca-certificates (20161130) ...
mktemp: failed to create file via template '/data/local/tmp/ca-certificates.crt.tmp.XXXXXX': No such file or directory
dpkg: error processing package ca-certificates (--configure):
 subprocess installed post-installation script returned error exit status 1
dpkg: dependency problems prevent configuration of python-pip:
 python-pip depends on ca-certificates; however:
  Package ca-certificates is not configured yet.
```

У меня вылечилось очень просто

```bash
mkdir -p /data/local/tmp/
```

И обновляемся дальше!

```bash
apt update && apt upgrade -y && apt dist-upgrade -y && apt autoremove
```