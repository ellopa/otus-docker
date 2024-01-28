## Docker

### Основная часть

1. Установите Docker на хост машину
https://docs.docker.com/engine/install/ubuntu/
2. Установите Docker Compose - как плагин, или как отдельное приложение
3. Создайте свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)
4. Определите разницу между контейнером и образом
5. Вывод опишите в домашнем задании.
6. Ответьте на вопрос: Можно ли в контейнере собрать ядро?
Собранный образ необходимо запушить в docker hub и дать ссылку на ваш репозиторий.

### Команды
>- docker ps - просмотреть список запущенных контейнеров
>- docker ps -a - просмотреть список всех контейнеров
>- docker run -d -p port:port container_name - запуск нового контейнера с пробросом портов
>- docker run --name test -d nginx:alpine - запуск нового контейнера с именем test с использованием nginx:alpine image в автономном режиме.
>- docker stop container_name - остановка контейнера
>- docker logs container_name - вывод логов контейнеров
>- docker inspect container_name - информация по запущенному контейнеру
>- docker build -t dockerhub_login/reponame:ver - билд нового образа
>- docker push/pull - отправка/получение образа из docker-registry
>- docker exec -it container_name bash - выполнить команду внутри оболочки контейнера (в данном примере мы выполняем команду “bash” внутри контейнера и попадаем в оболочку, внутрь контейнера)
>- docker search nginx   - Поиск образов в базе DockerHub
>- docker pull nginx     - скачать из репозитория 
>- docker build .  - Сборка Docker файла расположенного локально: 
>- docker images  - Все образы: 
>- docker ps -a   - Все контейнеры, которые когда-то запускались:
>- docker run -it 13bcb7e0ad0b - Запуск контейнера в консоли:
>- docker run -it -p 1234:80 nginx - Запуск контейнера с перенапавлением портов (на хосте веб-сервер запустится с http://localhost:1234) 
>- docker exec -it container_name /bin/sh    - Войти в контейнер с окружением /bin/sh
>- docker build -f Dockerfile -t my-server . - Собрать Image из Dockerfile и задать имя контейнера my-server 

>- docker run -itd 
>- **-it** сокращение от --interactive + --tty Когда вы запускаете docker с помощью этой команды, она переносит вас прямо внутрь контейнера.
>- **-d** сокращение от --detach, что означает, что вы просто запускаете контейнер, а затем отсоединяетесь от него. По сути, вы запускаете container в фоновом режиме.
>- **-itd** запускает оба параметра. В результате контейнер все равно будет работать в фоновом режиме, даже без запуска какого-либо приложения по умолчанию.

>-Докер хранит файлы (контейнеры) в виде папок и файлов в директории /var/lib/docker/containers 

### Шаблон для работы с Dockerfile

Что должно быть Dockerfile:
```
FROM image name
RUN apt update -y && apt upgrade -y
COPY или ADD filename /path/in/image
EXPOSE portopenning
CMD or ENTRYPOINT or both
```
- **не забываем про разницу между COPY и ADD**
- **or - одна из опций на выбор**

### 1/2. Установка Docker и Docker Compose на хост машину
-[Инструкция](https://docs.docker.com/engine/install/ubuntu/)

- Проверить соответсвие необходимым требованиям
```
elena_leb@ubuntunbleb:~/SELinux_DZ$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 22.04.3 LTS
Release:	22.04
Codename:	jammy
elena_leb@ubuntunbleb:~/SELinux_DZ$ uname -m
```
- Удалить старые версии ( первая установка, не требуется)

- Docker Engine зависит от контейнеров и запуска. Docker Engine объединяет эти зависимости в один пакет: contained.io. Если ранее устанавливали контейнеры или запускали, то  удалить их, чтобы избежать конфликтов с версиями, поставляемыми с Docker Engine.

- Запустить следующую команду, чтобы удалить все конфликтующие пакеты:
```
for pkg in docker.io docker-doc docker-compose docker-compose-v2 podman-docker containerd runc; do sudo apt-get remove $pkg; done
```
>- apt-get может сообщить, что у вас не установлен ни один из этих пакетов.

>- Образы, контейнеры, тома и сети, хранящиеся в /var/lib/docker/, не удаляются автоматически при удалении Docker. Если вы хотите начать с чистой установки и предпочитаете очистить все существующие данные, прочтите раздел "Удаление Docker Engine".

- Установить Docker Engine из репозитория apt Docker.
```
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
- Установить пакеты Docker.
```
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
- Проверить, запустив образ hello-world.
```
sudo docker run hello-world
```
```
elena_leb@ubuntunbleb:~/SELinux_DZ$ sudo docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete 
Digest: sha256:4bd78111b6914a99dbc560e6a20eab57ff6655aea4a80c50b0c5491968cbc2e6
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```
>- Группа пользователей docker существует, но не содержит пользователей, поэтому вам необходимо использовать sudo для запуска команд Docker.
>- Можно настроить управление Docker от имени пользователя, не являющегося root
>- Добавление user'a в группу docker (исправлет ошибку при подключении к сокету / позволит запускать docker без sudo):
```
sudo usermod -aG docker $(whoami)
sudo usermod -aG docker $USER
```
>- [Manage Docker as a non-root user](https://docs.docker.com/engine/install/linux-postinstall/#manage-docker-as-a-non-root-user)

### 3. Создать свой кастомный образ nginx на базе alpine. После запуска nginx должен отдавать кастомную страницу (достаточно изменить дефолтную страницу nginx)

- Создать [Dockerfile](https://github.com/ellopa/otus-docker/blob/main/Dockerfile)

- Билдим
```
elena_leb@ubuntunbleb:~/DOCKER_DZ$ sudo docker build -t ellopa/my-server-nginx .
[sudo] пароль для elena_leb: 
[+] Building 5.7s (9/9) FINISHED                                                                                      docker:default
 => [internal] load build definition from Dockerfile                                                                            0.0s
 => => transferring dockerfile: 221B                                                                                            0.0s
 => [internal] load metadata for docker.io/library/alpine:3.13                                                                  2.7s
 => [internal] load .dockerignore                                                                                               0.0s
 => => transferring context: 2B                                                                                                 0.0s
 => [internal] load build context                                                                                               0.0s
 => => transferring context: 1.80kB                                                                                             0.0s
 => [1/4] FROM docker.io/library/alpine:3.13@sha256:469b6e04ee185740477efa44ed5bdd64a07bbdd6c7e5f5d169e540889597b911            0.7s
 => => resolve docker.io/library/alpine:3.13@sha256:469b6e04ee185740477efa44ed5bdd64a07bbdd6c7e5f5d169e540889597b911            0.0s
 => => sha256:469b6e04ee185740477efa44ed5bdd64a07bbdd6c7e5f5d169e540889597b911 1.64kB / 1.64kB                                  0.0s
 => => sha256:16fd981ddc557fd3b38209d15e7ee8e3e6d9d4d579655e8e47243e2c8525b503 528B / 528B                                      0.0s
 => => sha256:6b5c5e00213a401b500630fd8a03f6964f033ef0e3a6845c83e780fcd5deda5c 1.47kB / 1.47kB                                  0.0s
 => => sha256:72cfd02ff4d01b1f319eed108b53120dea0185b916d2abeb4e6121879cbf7a65 2.83MB / 2.83MB                                  0.6s
 => => extracting sha256:72cfd02ff4d01b1f319eed108b53120dea0185b916d2abeb4e6121879cbf7a65                                       0.0s
 => [2/4] RUN apk update && apk add nginx                                                                                       2.1s
 => [3/4] COPY ./nginx.conf /etc/nginx/nginx.conf                                                                               0.0s
 => [4/4] COPY ./*.html /usr/share/nginx/html/                                                                                  0.1s
 => exporting to image                                                                                                          0.0s 
 => => exporting layers                                                                                                         0.0s 
 => => writing image sha256:a7d97e79e90317bab690a19cacc9c2063036832b85af55d95e6ceb35446b84f4                                    0.0s 
 => => naming to docker.io/ellopa/my-server-nginx    
```
- Запустить контейнер

```
root@ubuntunbleb:~# docker run -it  -p 8081:80 --name cont_nginx_3 ellopa/my-server-nginx
```
```
root@ubuntunbleb:~# docker ps -a
CONTAINER ID   IMAGE                    COMMAND                  CREATED              STATUS                      PORTS                                            NAMES
56aa7d191925   ellopa/my-server-nginx   "nginx -g 'daemon of…"   About a minute ago   Up About a minute           443/tcp, 0.0.0.0:8081->80/tcp, :::8081->80/tcp   cont_nginx_3

```
- Запуск еще одного с опциями -itd
```
root@ubuntunbleb:~# docker run -itd  -p 8081:80 --name cont_nginx_4 ellopa/my-server-nginx
4beba65b94036ef479ae51dd8101b7a0b54de6026a81ebd6565fa971d205c9bb
```
- Посмотреть  запущенные контейнеры. 
```
root@ubuntunbleb:~# docker ps
CONTAINER ID   IMAGE                    COMMAND                  CREATED              STATUS              PORTS                                            NAMES
4beba65b9403   ellopa/my-server-nginx   "nginx -g 'daemon of…"   About a minute ago   Up About a minute   443/tcp, 0.0.0.0:8081->80/tcp, :::8081->80/tcp   cont_nginx_4
```
>- Поле PORTS в выходных данных сообщает, что порт 80 на хосте Docker сопоставлен с портом 80 в контейнере. Другой способ убедиться, что NGINX запущен, - это отправить HTTP-запрос на этот порт. Появится код страницы приветствия NGINX

```
root@ubuntunbleb:~# curl http://localhost
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>

</head>
<body>

    <div align="center">
        <font color="green">
          Hello from Nginx on Alpine container
          <p id="date"></p>
          <p id="time"></p>
        </font>
      </div>
<script>
n =  new Date();
y = n.getFullYear();
m = n.getMonth() + 1;
d = n.getDate();
t = n.toLocaleTimeString();
document.getElementById("date").innerHTML = "Date: " + m + "/" + d + "/" + y;
document.getElementById("time").innerHTML = "Time: " + t;
</script>
</body>
```
![screenshot1](https://github.com/ellopa/otus-docker/blob/main/scr_1.png)

**Отображается измененная страница. Cобран кастомный образ nginx на базе alpine [Dockerfile](https://github.com/ellopa/otus-docker/blob/main/Dockerfile). После запуска nginx отдает кастомную страницу из файла [index.html](https://github.com/ellopa/otus-docker/blob/main/index.html). Конфигурация nginx - [nginx.conf](https://github.com/ellopa/otus-docker/blob/main/nginx.conf). Файлы копируются в контейнер при сборке.**  

### 4. Определите разницу между контейнером и образом

- Docker контейнер — это самодостаточная и изолированная среда, содержащая все необходимое для работы приложения. Он представляет собой легковесный пакет, который включает в себя код приложения, утилиты, зависимости и всю другую необходимую систему для его работы.
В отличие от традиционных виртуальных машин, Docker контейнеры не имитируют полноценное аппаратное обеспечение, а вместо этого используют общую операционную систему с хост-машиной. Docker контейнеры используют контейнеризацию на уровне ОС (такую как LXC или libcontainer), что позволяет им быть легковесными и запускаться очень быстро.
Контейнеры создаются из образов с помощью команды `docker run`. Команда `docker run` берет Docker образ в качестве темплейта и создает из него контейнер, который и запускается.
- Образ используется в качестве шаблона для создания контейнеров. Несколько контейнеров могут быть запущены, используя один и тот же образ. Сам по себе Docker-образ невозможно «запускать», запускаются контейнеры, внутри которых работает приложение. Образы можно хранить в Docker Repository, например, Docker Hub или GitLab, откуда образы можно загрузить на хостовую систему.
Чтобы создать Docker-контейнер необходимо собрать образ - берется какой-то базовый образ, например Alpine, CentOS или Ubuntu, в него с помощью специальных команд зашивается приложение и затем выгружается уже туда, где оно будет работать. 
Образы создаются из Dockerfile с помощью команды `docker build`.

### 6. Сборка ядра в контейнере.
Возможна. 
- Да, возможно. Примеры сборки ядра в контейнере: 
[ 1 ]((https://github.com/a13xp0p0v/kernel-build-containers)
[ 2 ](https://hub.docker.com/r/tomzo/buildkernel)
[ 3 ](https://github.com/tomzo/docker-kernel-ide)

## Загрузка образа в DockerHub 

- Просмотр образов.  
```
lena_leb@ubuntunbleb:~$ sudo docker images

REPOSITORY               TAG       IMAGE ID       CREATED        SIZE
ellopa/my-server-nginx   latest    4fa8208ac64c   4 days ago     9.04MB
```
>- В названии образа сразу было добавлено название репозитория Docker Hub. Если создан локальный, то можно создать тэг для него , например **docker tag 0e5574283393 fedora/httpd:version1.0** - пометит локальный образ с идентификатором 0e5574283393 как fedora/httpd с тегом version1.0:  

- Залогинится на Dcker Hub и выполнить пуш - docker push ellopa/my-server-nginx:latest
```
elena_leb@ubuntunbleb:~$ docker login
Log in with your Docker ID or email address to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com/ to create one.
You can log in with your password or a Personal Access Token (PAT). Using a limited-scope PAT grants better security and is required for organizations using SSO. Learn more at https://docs.docker.com/go/access-tokens/

Username: ellopa
Password: 
WARNING! Your password will be stored unencrypted in /home/elena_leb/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```
```
elena_leb@ubuntunbleb:~$ docker push ellopa/my-server-nginx:latest
The push refers to repository [docker.io/ellopa/my-server-nginx]
e608957a81b1: Pushed 
b060b6bb2377: Pushed 
62243e0305ce: Pushed 
4cd7317e69db: Pushed 
7df5bd7bd262: Mounted from library/alpine 
latest: digest: sha256:b4df6e1faba85a7b9a7c0616da26c4738bc0b5f8d3cbee8ff5b20d613dae8982 size: 1359
```
- [Репозиторий](https://hub.docker.com/repository/docker/ellopa/my-server-nginx/general).

