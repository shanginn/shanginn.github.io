---
title: Простой способ делать простые HTTP запросы на PHP
layout: post
category: tips
tags:
- tips
- PHP
- Laravel
---

Хочу делать простые `post($url, $jsonBody)` и `get($url)` без долгих настроек и подготовок.

# Intro

У JS есть [fetch()](https://www.npmjs.com/package/node-fetch) и [axios](https://github.com/axios/axios) -
замечательные библиотеки для HTTP запросов. 

У PHP есть curl с ["великолепным"](https://www.php.net/manual/ru/function.curl-setopt.php) API использовать
который чисто теоретически возможно использовать, но связываться с ним не хочется.

Ещё есть [Guzzle](https://github.com/guzzle/guzzle), который совместим с [PSR-18](https://www.php-fig.org/psr/psr-18/)
и даёт нам `sendRequest`.

Но я хочу делать `->post($url, $jsonBody)` и `->get($url)`. Хочу синтаксического сахара.

Также рассуждал автор [zttp](https://github.com/kitetail/zttp),
но проект [был поглощён Laravel](https://laravel-news.com/zttp-is-coming-to-laravel-7)
и больше не поддерживается.

Есть логическое продолжение `zttp`: [xttp](https://github.com/jr-smith/xttp),
но он тоже больше не поддерживается.

Я уже почти бросился писать свою обёртку, но решил поискать ещё немного и наткнулся на 
проект [Torch](https://github.com/mattstauffer/Torch), который приводит примеры,
как использовать компоненты Laravel вне проекта на Laravel.

И там есть пример для [http клиента Laravel](https://github.com/mattstauffer/Torch/tree/master/components/http).
То что нужно!

## Так и как?

```bash
composer req guzzlehttp/guzzle illuminate/http
```

Клиент:
```php
$http = new \Illuminate\Http\Client\Factory();
```

POST:
```php
$response = $http->post('https://jsonplaceholder.typicode.com/posts', [
    'title' => 'foo',
    'body' => 'bar',
    'userId' => 1,
]);

$id = $response->json()['id'];
```

GET:
```php
$response = $http->get('https://jsonplaceholder.typicode.com/posts');
echo $response->body();
```

![👌]({{ site.basepath }} /images/posts/ok-pacha.png)
