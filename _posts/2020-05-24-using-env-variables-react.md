---
layout: post
title:  "Использование env файлов в React"
date:   2020-05-24 00:00:00 +0300
categories: react env
---

Иногда в react-приложении требуется использовать переменные окружения, например это может быть API url, либо какой-то конфиг или какие-то API ключи.
Есть 2 способа задать переменные окружения:
 * через командную строку (временное решение в текущей сессии).
 * через `.env` файлы.

## Create React App (CRA)

В случае использования CRA, ничего настраивать не требуется. Под капотом уже установлены необходимые пакеты и настроен webpack для обработки таких файлов.
Обязательным условием является только префикс `REACT_APP_` для переменных окружения. Например, `REACT_APP_API_KEY=secret-api-key`. Другие имена переменных будут игнорироваться, чтобы избежать случайного раскрытия секретного ключа, который может иметь то же имя. (Исключение составляет только `NODE_ENV`).


Чтобы использовать переменную в коде, нужно обратиться к ней так:
```
process.env.REACT_APP_API_KEY
```

Другой способ задания переменных через командную строку (Bash):
```
REACT_APP_NOT_SECRET_CODE=abcdef npm start
```

## Свой webpack конфиг

В этом случае нужно установить плагин [dotenv-webpack](https://www.npmjs.com/package/dotenv-webpack). 
Затем добавить в конфиг:
```
// webpack.config.js
const Dotenv = require('dotenv-webpack');
 
module.exports = {
  ...
  plugins: [
    new Dotenv()
  ]
  ...
};
```

Также следует добавить .env.local файлы в `.gitignore`:
```
# misc
.env.local
```

## Виды .env файлов

* `.env`: По умолчанию.
* `.env.local`: Локальные переопределения. Этот файл загружается во всех окружениях, кроме test.
* `.env.development`, `.env.test`, `.env.production`: Настройки для конкретной среды.

* `.env.development.local`, `.env.test.local`, `.env.production.local`: Локальные переопределения настроек для конкретной среды.

Файлы слева имеют больший приоритет, чем файлы справа.

* `npm start`: `.env.development.local`, `.env.development`, `.env.local`, `.env`
* `npm run build`: `.env.production.local`, `.env.production`, `.env.local`, `.env`
* `npm test`: `.env.test.local`, `.env.test`, `.env` (`.env.local` не участвует).


### Разное

После добавления `.env` файлов при запущенном `webpack-dev-server`, его следует перезапустить.

Иногда встречается ошибка, когда значения переменной заключают в кавычки - так работать не будет.
```
// Wrong 
REACT_APP_KEY="TEST"
// Right
REACT_APP_KEY=TEST
```

Обычно `.env` файлы лежат в корне проекта.

Чтобы загрузить `.env` файл, в зависимости от окружения, в параметрах плагина можно указать переменную окружения `NODE_ENV`:

```
module.exports = env => {
  plugins: [
    new Dotenv({
      path: `./.env.${env.NODE_ENV}`,
      safe: false
    }),
   ]
};
``` 

#### Развертывание переменных окружения

Развертывание переменных уже присутствующих в системе в `.env` файле, используя [dotenv-expand](https://github.com/motdotla/dotenv-expand)).

Например, чтобы получить переменную окружения `npm_package_version`:

```
REACT_APP_VERSION=$npm_package_version
# also works:
# REACT_APP_VERSION=${npm_package_version}
```

Или раскрыть переменные локально в текущем `.env` файле:

```
DOMAIN=www.example.com
REACT_APP_FOO=$DOMAIN/foo
REACT_APP_BAR=$DOMAIN/bar
```
