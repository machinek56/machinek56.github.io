---
layout: post
title:  "Как собрать свой npm-пакет из форка"
date:   2020-04-18 00:00:00 +0300
categories: npm github
---

В ходе разработки иногда может потребоваться внести в какую-нибудь библиотеку на гитхабе свой функционал,
который нужен только на конкретном проекте или же библиотека давно не поддерживается и pull-request'ы никак не принимают.
В таких случаях выходом из ситуации является создание своего форка библиотеки.

Один из вариантов решения проблемы является публикация *dist* сборки на гитхаб, но такой подход не всегда 
можно использовать из-за ограничений стендов сборки проекта.

Пример записи в *package.json*: 
```bash
"redux-saga-router": "luistorres/redux-saga-router#dist"
```

Другой вариант заключается в создании npm-пакета со своим scope (@username). Обычно берут свой username на гитхабе, но по сути можно написать 
все что угодно. Для публикации в npm потребуется зарегистрировать там аккаунт.

Далее нужно поменять имя пакета на `@username/package-name` и увеличить патч версию пакета в *package.json*.
После этого переходим в консоль:
```bash
npm login
```
Вводим свои учетные данные.

Далее пишем команду для публикации:
```bash
npm publish
```
Однако так как мы используем scope, нужно дописать флаг `--access public`

```bash
npm publish --access public
```

После окончания процедуры устанавливаем новый пакет в свой проект:
```bash
npm i @username/package-name
```

----
<br/>
В моей практике понадобилось завернуть широко используемую в проекте библиотеку в форк. 
Для этого мне нужно было бы поменять все импорты на другое имя пакета. Чтобы этого избежать, в npm есть возможность установить пакет под другим именем (алиасом):  

```bash
npm i <alias_name>@npm:<original_package_name>
```

Таким образом имя меняется только в *package.json*. Например:
```bash
 "redux-form": "npm:@machinek56/redux-form@^8.3.1",
```
