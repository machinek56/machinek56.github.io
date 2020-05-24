---
layout: post
title:  "Создание виртуальных хостов в MAMP"
date:   2019-09-20 21:30:00 +0300
categories: macos mamp php
---

Чтобы поднять виртуальный хост на локальной машине, используя MAMP, необходимо: 

### Добавляем домен в hosts

Придумать имя хоста, например, *domain.local, domain.loc или domain.test*.

Открыть файл *hosts* через консоль `sudo nano /etc/hosts` и дописать имя хоста в строку с адресом 127.0.0.1, сохранить изменения и выйти.

```
127.0.0.1    localhost domain.local
```

### Добавляем виртуальные хосты в apache конфиг

Перейти в директорию `/Applications/MAMP/conf/apache/extra/` и открыть в редакторе файл *httpd-vhosts.conf*.

Найти dummy примеры, удалить и добавить следующее:

```
<VirtualHost *:80>
    DocumentRoot /Applications/MAMP/htdocs
    ServerName localhost
</VirtualHost>


<VirtualHost *:80>
    DocumentRoot "/Users/{username}/path/to/project"
    ServerName domain.local
</VirtualHost>
```
Заменить *DocumentRoot* и *ServerName* своими значениями.

### Включение виртуальных хостов в общий конфиг

Далее открыть файл `Applications/MAMP/conf/apache/httpd.conf` и найти строчку 

```
# Virtual Hosts
# Include /Applications/MAMP/conf/apache/extra/httpd-vhosts.conf

```
Раскомментировать вторую строчку с Include и сохранить файл.

### Разрешаем FollowSymLinks

В этом же файле *httpd.conf* найти следующий фрагмент:

```
<Directory />
    Options Indexes FollowSymLinks
    AllowOverride None
</Directory>
```

Заменить *None* на *All*.

```
<Directory />
  Options Indexes FollowSymLinks
  AllowOverride All
</Directory
```

### Установка порта 80 в apache

Установить порт apache 80 вместо 8888 в настройках MAMP.

На этом все, осталось перезапустить процессы MAMP и все должно заработать.



