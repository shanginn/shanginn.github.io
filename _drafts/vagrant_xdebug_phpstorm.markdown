---
title: PHP if test
layout: post
---

|       $elem      | isset($elem); | empty($elem); | $elem ? true : false; | isset($elem) && !empty($elem); |
| ---------------- | ------------- | ------------- | --------------------- | ------------------------------ |
| `string ''`      | true          | true          | false                 | false                          |
| `boolean false`  | true          | true          | false                 | false                          |
| `boolean true`   | true          | false         | true                  | true                           |
| `int 0`          | true          | true          | false                 | false                          |
| `int 1`          | true          | false         | true                  | true                           |
| `string '0'`     | true          | true          | false                 | false                          |
| `string '1'`     | true          | false         | true                  | true                           |
| `Not in array`   | false         | true          | false                 | false                          |

