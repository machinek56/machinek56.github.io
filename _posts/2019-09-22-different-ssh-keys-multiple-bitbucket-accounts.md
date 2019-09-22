---
layout: post
title:  "Использование разных ssh ключей для нескольких Bitbucket аккаунтов"
date:   2019-09-22 20:30:00 +0300
categories: git bitbucket ssh
---

Понадобилось как-то использовать 2 аккаунта на Bitbucket - личный и рабочий. 
И при добавлении в рабочий существующего ssh ключа возникла ошибка, что данный ключ используется уже на другом аккаунте.
После долгих часов гугления и попыток, в итоге получилось решить эту проблему.

#### Создать ключи для каждого аккаунта

Генерируем новую пару ключей с помощью команды ниже в директории ` ~/.ssh/`

```
 ssh-keygen -t rsa -C "user1" -f "user1"
```

Ключ -C означает комментарий чтобы обозначить ssh-ключ. Ключ -f указывает на имя файла.
Нужно создать ключи для всех аккаунтов, которые вы хотите добавить.

#### Добавить публичный ключ в аккаунты Bitbucket

Заходим в настройки аккаунта Bitbucket и добавляем ssh-ключ.

#### Конфигурируем SSH

В папке `~/.ssh/` создаем файл с названием **config** и вставляем туда конфиг ниже. 

```
 #user1 account
 Host bitbucket.org-user1
     HostName bitbucket.org
     User git
     IdentityFile ~/.ssh/user1
     IdentitiesOnly yes

 #user2 account
 Host bitbucket.org-user2
     HostName bitbucket.org
     User git
     IdentityFile ~/.ssh/user2
     IdentitiesOnly yes
```
Заменяем *user1* и *user2* соответственно нужными значениями.

#### Конфигурируем Git репозиторий

Если нет локальной копии репозитория, то нужно склонировать его следующей командой:

```
 git clone git@bitbucket.org-user1:user1/your-repo-name.git    
```

Если репозиторий уже склонирован, то нужно обновить origin:

```
git remote set-url origin git@bitbucket.org-user1:user1/your-repo-name.git
```

Если нужно поменять имя пользователя или email, то в папке проекта выполнить следующие действия:

```
git config user.name "user1"
git config user.email "user1@example.com"
```

Заменить *user1* именем из конфига.

Теперь при попытке склонировать или обновить репозиторий ошибки быть не должно.

