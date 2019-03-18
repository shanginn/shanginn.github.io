---
title: Laravel tinker (psysh) моментально закрывается на PHP 7.3 (возможно, только на MacOS Mojave)
layout: post
category: help
tags:
- Laravel
- PHP
- fix
---

Обновились на PHP 7.3 и перестал работать Tinker? Я столкнулся с той же проблемой. Решения пока нет, но есть workaround.

Точнее, он работает, но закрывается после выполнения любой команды без ошибок. Tinker основан на PsySH консоле и нам нужно подправить его [конфиг](https://github.com/bobthecow/psysh/wiki/Configuration). На маке он находится тут: `~/.config/psysh/config.php`. Прописать нужно следующее:

```php
<?php

return [
  'usePcntl' => false,
];
```

Сделать это можно одной командой:

```bash
echo "<?php

return [
  'usePcntl' => false,
];" > ~/.config/psysh/config.php
```

И всё будет работать.