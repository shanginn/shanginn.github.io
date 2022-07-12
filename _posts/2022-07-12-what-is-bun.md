---
title: Что такое Bun?
layout: post
category: what-is
tags:
- what-is
- bun
- javascript
- typescript
---

[bun](https://bun.sh/) - это новая среда для исполнения серверного js/ts кода, быстрее [nodejs](https://nodejs.org/en/) в не сколько раз. И в отличии от [deno](https://deno.land/) нацелен на полную обратную совместимость с node, что позволит Bun запускать всё существующие богатство JS-кода без каких-либо доработок.

## Как так быстро?

Bun написан с нуля на низкоуровневом языке [Zig](https://ziglang.org/) с ручным контролем памяти, что и позволило автору так оптимизировать свою разработку. Помимо этого, под копотом он использует движок [JavaScriptCore](https://github.com/WebKit/WebKit/tree/main/Source/JavaScriptCore), вместо традиционного [V8](https://v8.dev/), что позволяет ему запускаться [гораздо быстрее](https://twitter.com/jarredsumner/status/1499225725492076544).

## Что в коробке?

Кроме рантайма bun включает в себя и другие полезные вещи, которые в node идут отдельными пакетами:
- Некоторые Web API, например, `fetch`, `WebSocket` и `ReadableStream`
- Менеджер пакетов: `bun install`. Также bun реализует алгоритм разрешения зависимостий а-ля Node.js, так что вы можете использовать npm пакеты.
- Бандлер (типа webpack): `bun bun`
- Среда для тестов, а-ля Jest
- Более быстрый способ писать в файловую систему: `Bun.write`
- Автоматическая подгрузка `.env` файлов.
- Встроенный `sqlite3` клиент
- Вызов функций из других языков (foreign function interface): `bun:ffi`
- Нативная поддержка некоторых модулей Node.js, например, `node:fs` и `node:path`
- И многое другое


## Ставим и запускаем

```bash
curl https://bun.sh/install | bash
```

Сделаем вебсервер:
```js
// http.js
export default {
  port: 3000,
  fetch(request) {
    return new Response("Hi Bun!");
  },
};
```

У меня такой простейший веб сервер мгновенно обработал 10000 запросов:
```
$ ab -c 10 -n 10000 http://127.0.0.1:3000/
This is ApacheBench, Version 2.3 <$Revision: 1879490 $>
Copyright 1996 Adam Twiss, Zeus Technology Ltd, http://www.zeustech.net/
Licensed to The Apache Software Foundation, http://www.apache.org/

Benchmarking 127.0.0.1 (be patient)
Completed 1000 requests
Completed 2000 requests
Completed 3000 requests
Completed 4000 requests
Completed 5000 requests
Completed 6000 requests
Completed 7000 requests
Completed 8000 requests
Completed 9000 requests
Completed 10000 requests
Finished 10000 requests


Server Software:
Server Hostname:        127.0.0.1
Server Port:            3000

Document Path:          /
Document Length:        7 bytes

Concurrency Level:      10
Time taken for tests:   0.443 seconds
Complete requests:      10000
Failed requests:        0
Total transferred:      850000 bytes
HTML transferred:       70000 bytes
Requests per second:    22590.19 [#/sec] (mean)
Time per request:       0.443 [ms] (mean)
Time per request:       0.044 [ms] (mean, across all concurrent requests)
Transfer rate:          1875.16 [Kbytes/sec] received

Connection Times (ms)
              min  mean[+/-sd] median   max
Connect:        0    0   0.1      0       1
Processing:     0    0   0.1      0       2
Waiting:        0    0   0.1      0       1
Total:          0    0   0.1      0       2

Percentage of the requests served within a certain time (ms)
  50%      0
  66%      0
  75%      0
  80%      0
  90%      1
  95%      1
  98%      1
  99%      1
 100%      2 (longest request)
```

# Выводы
Штука очень перспективная, будем посмотреть за развитием. Но помните, что проект только что вышел в бете, так что подождите недельку перед использованием в проде.