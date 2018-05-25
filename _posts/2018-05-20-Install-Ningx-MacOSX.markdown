---
layout: post
title:  "Настройка Nginx, PHP в Mac OS X"
date:   2018-05-20 17:40:54 +0300
categories: jekyll post
---
Потребовалось мне однажды настроить окружение на маке для одного проекта на PHP. Впервые столкнувшись с этим полез в гугл за мануалами. Но как это обычно бывает, с первого раза не завелось, и со второго тоже, и с пятого.. Так прошло несколько часов и вечер плавно перетек в ночь..

На следующий день после некоторых телодвижений все заработало, и я хочу оставить свой мануал по настройке сие тут.

Задача состояла в следующем: настроить виртуальный хост на папку /public проекта, подняв Nginx. Говно вопрос.

На моем маке уже стоял менеджер пакетов **Homebrew**, но если у вас его нет, скачать можно  [отсюда](https://brew.sh/index_ru)

## Установка

1. Ставим nginx с помощью brew
```
brew install nginx
```
2. Запускаем nginx
```
sudo nginx
```
3. Открываем в браузере ссылку, если видим приветственную надпись *Welcome to Nginx*, значит nginx запущен и все хорошо.
```
http://127.0.0.1:8080/
```
4. Ставим PHP также с помощью brew. В Mac OS X PHP уже предустановлен, но можно и обновить.
```
brew install php
```

## Конфигурация

Конфиг nginx по умолчанию находится по пути `/usr/local/etc/nginx/`

Создаем папки для локальных виртуальных хостов
```
mkdir -p /usr/local/etc/nginx/sites-{enabled,available}
```

Заменяем дефолтный конфиг *nginx.conf* этим:

```
worker_processes  1;

error_log  /usr/local/etc/nginx/logs/error.log debug;

events {
    worker_connections  256;
}

http {
    include             mime.types;
    default_type        application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /usr/local/etc/nginx/logs/access.log  main;

    sendfile            on;

    keepalive_timeout   65;

    index index.html index.php;

    include /usr/local/etc/nginx/sites-enabled/*;
}
```

Создаем файл project.local в sites-available
```
touch /usr/local/etc/nginx/sites-available/project.local
```
Вставляем туда конфиг
```
server {
  listen                80;
  listen                [::]:80;
  server_name           project.local;

  location / {
    root /Users/{username}/Projects/project/public/;
    try_files  $uri  $uri/  /index.php?$args;
    index  index.html index.htm index.php;
  }

  location ~ \.php$ {
    root  /Users/{username}/Projects/project/public/;
    try_files  $uri  $uri/  /index.php?$args;
    index  index.html index.htm index.php;

    fastcgi_param PATH_INFO $fastcgi_path_info;
    fastcgi_param PATH_TRANSLATED $document_root$fastcgi_path_info;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;


    fastcgi_pass 127.0.0.1:9000;
    fastcgi_index index.php;
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_intercept_errors on;
    include fastcgi_params;
  }

}
```

Линкуем наш хост в sites-enabled (создаем симлинк)
```
ln -s /usr/local/etc/nginx/sites-available/project.local /usr/local/etc/nginx/sites-enabled
```

Добавляем наш хост в hosts `nano /etc/hosts`
```
# Nginx sites

127.0.0.1        project.local
```

Тестим конфиг nginx
```
sudo nginx -t
```
И если ошибок/предупреждений нет, то обновляем новый конфиг nginx
```
brew services reload nginx
```

Проверяем доступность созданного хоста
```
curl -IL http://project.local:8080
```
Если видим что-то похожее ниже, то открываем в браузере `project.local` и радуемся жизни!
```
HTTP/1.1 200 OK
Server: nginx/1.13.12
Date: Sun, 20 May 2018 21:07:25 GMT
Content-Type: text/html
Content-Length: 612
Last-Modified: Tue, 10 Apr 2018 14:11:19 GMT
Connection: keep-alive
ETag: "5accc607-264"
Accept-Ranges: bytes
```

На этом установка и настройка окружения завершена. По возникшим проблемам всегда можно обращаться к всезнающему [Google](https://google.ru)
