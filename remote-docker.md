# Работа с Docker на удалённом сервере

## 1. Подготовка сервера

### 1.1. Создание одноимённого пользователя

```console
remote-machine# useradd <USER-NAME>
```

### 1.2. Добавление пользователя в группу docker

```console
remote-machine# gpasswd -a <USER-NAME> docker
```

## 2. Запуск команд докера на удалённом сервере с локальной машины

```console
local-machine$ DOCKER_HOST=ssh://<HOST> docker <DOCKER-COMMND>
```

### 2.1. Создание контекста

```console
local-machine$ docker context create <CONTEXT-NAME> --description "<CONTEXT-DESCRIPTION>" --docker "host=ssh://<HOST>"
```

### 2.2. Просмотр контекстов

```console
local-machine$ docker context ls
```

### 2.3. Переключение контекста

```console
local-machine$ docker context use <CONTEXT-NAME>
```

## Ссылки

[How to Connect to Remote Docker using docker context CLI](https://collabnix.com/how-to-connect-to-remote-docker-using-docker-context-cli/)
