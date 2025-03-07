---
title: Как получить первый символ строки на PHP?
layout: post
category: lesson
tags:
- lesson
- PHP
---
Подробнее разберёмся с этим казалось бы простым вопросом.

В PHP можно обращаться к строке, как к массиву символов, но те, кто решили сделать это через `$str[0]` будут правы только в некоторых случаях. Так как вы читаете этот текст на русском языке, использовать такой метод вам строго противопоказано, дальше поймёте почему.

Вариант с `substr($str, 0, 1)` уже чуть ближе к истине, но у него такая же проблема, как и у первого метода: они оба получат символ из **первого байта** строки, а значит сработают только если символ влезает в один байт, или проще говоря - этот метод сработает только на английских словах.

Мы же живём в этоху победившего UTF8, в которой один символ может занимать от 1 до 4 байт например:
```php
# ❯ php -a
# Interactive shell

php > echo bin2hex('s')
# 73 // 1 байт

php > echo bin2hex('ъ');
# d1 8a // 2 байта

php > echo bin2hex('€');
# e2 82 ac // 3 байта

php > echo bin2hex('𐍈');
# f0 90 8d 88 // 4 байта
```

А вот, что мы получим, если попробуем получить первый символ, обращаясь к строке как к массиву:

```php
php > echo 's'[0];
# s // HEX у этого символа всё ещё 73, поэтомы мы получаем нормальную букву s
php > echo 'ъ'[0];
# � // Здесь от 'ъ' остался только первый байт, `d1`, у которого нет соответствующего символа
php > echo '€'[0];
# � // Та же история, осталось только e2
php > echo '𐍈'[0];
# � // Тут взяли только f0
```

Абсолютно то же самое произойдёт, если использовать `substr($str, 0, 1)`. Если кто-то ещё сомневается, попробуйте выполнить `echo substr('ъ', 0, 1) . 'ъ'[1];` и посмотрите, что получится.

Для работы с мультибайтовым кодировками в PHP есть [целый набор функций](https://www.php.net/manual/ru/ref.mbstring.php), который начинаются на `mb_`, в данном случае мы можем воспользоваться функцией *mb_substr*, чтобы получить первый символ строки:
```php
php > echo mb_substr('s', 0, 1);
# s
php > echo mb_substr('ъ', 0, 1);
# ъ
php > echo mb_substr('€', 0, 1);
# €
php > echo mb_substr('𐍈', 0, 1);
# 𐍈
```

`mb_substr` может работать даже с эмоджи, которые могут состоять из нескольких UTF8 символов. Но [эмоджи могут быть составными](https://unicode.org/emoji/charts/emoji-zwj-sequences.html), то есть состоять из нескольких эмоджи (модификаторы цвета кожи, или пола) и с такими эмоджи mb_substr будет возвращать только первую пиктограмму:
```php
php > echo mb_substr('👍😅🤡', 0, 1);
// 👍
php > echo mb_substr('😵‍💫', 0, 1);
// 😵
php > echo mb_substr('😮‍💨', 0, 1);
// 😮
```

Но это уже совсем другая история.
