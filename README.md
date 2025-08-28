# Установка и запуск NGINX и серверного приложения с помощью Docker

## Зависимости

- Docker

## Установка и настройка

### 1. Установка Docker

`sudo pacman -S docker docker-compose docker-buildx`

- Установка докера в **Arch Linux**

### 2. Запуска Nginx

#### Docker + Nginx

```bash
docker run --name docker-server -p 8070:80 -d nginx:latest
```

- Запуск контейнера с помощью образа `nginx:latest`
- Проброс порта `80` с контейнера на порт `8070` локальной машины
- `-d` или `--detach`освобождает терминал и запускает контейнер в фоновом режиме

#### Управление контейнерами

```bash
docker ps
docker ps -a
```

- Проверка запущенных и остановленных контейнеров

```bash
docker rm -f <container_name>
```

- Удаление контейнера по имени контейнера

#### Запуска контейнера с монтированием файла

```bash
docker run --name nginx-server -p 8070:80 -v ~/dev/github/nginx-basic-1/html/:/usr/share/nginx/html -d nginx:latest
```

- Монтирование файла **html/index.html** в контейнер

### 3. Создание образа с помощью Dockerfile

```bash
docker build -t backendapp:0.1 .
```

- В конце вместо точки можно указать где находится `Dockerfile`

### 4. Создание Docker-сети

```bash
docker network create backend-network
```

### 5. Создание контейнеров с привязкой к созданной сети

```bash
docker run --network=backend-network --name backendapp1 -p 8081:8090 --hostname=backendapp1 -d backendapp:0.1
docker run --network=backend-network --name backendapp2 -p 8082:8090 --hostname=backendapp2 -d backendapp:0.1
docker run --network=backend-network --name backendapp3 -p 8083:8090 --hostname=backendapp3 -d backendapp:0.1
docker run --network=backend-network --name backendapp4 -p 8084:8090 --hostname=backendapp4 -d backendapp:0.1
docker run --network=backend-network --name backendapp5 -p 8085:8090 --hostname=backendapp5 -d backendapp:0.1
```

- Создание пяти контейнеров с привязкой к созданной сети

### 6. Создание nginx контейнера который проксирует наши сервисы

```bash
docker run --network=backend-network --name nginx-server -p 8080:8080 -v ~/dev/github/nginx-basic-1/nginx.conf/:/etc/nginx/nginx.conf -d nginx:latest
```

Можно поднять второй `nginx` контейнер, для масштабировании самого `nginx`, для этого надо изменить название контейнера и порт в котором он работает, например:

```bash
docker run --network=backend-network --name nginx-server2 -p 8070:8080 -v ~/dev/github/nginx-basic-1/nginx.conf/:/etc/nginx/nginx.conf -d nginx:latest
```

### 7. Утилита wrk

```
wrk -t12 -c400 -d30s http://127.0.0.1:8080
```

- Бенчмарк нашего nginx сервера
