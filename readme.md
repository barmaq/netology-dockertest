вывод из консоли и скриншоты в расположенных рядом файлах

---------------------------------------------------------
задание 1

установка docker и docker-compose по мануалу докера
https://docs.docker.com/engine/install/

docker pull nginx:1.21.1
mkdir nginx_custom
cd nginx_custom
nano index.html
	вставляем предложенное содержимое. не привожу чтобы не повыставлять заголовков в файле.
nano dockerfile
	FROM nginx:1.21.1
	COPY index.html /usr/share/nginx/html/index.html

docker build -t barmaq/custom-nginx:1.0.0 .
docker login
docker push barmaq/custom-nginx:1.0.0

https://hub.docker.com/repository/docker/barmaq/custom-nginx

------------------------------------------------------
задание 2
контейнер опубликован на порту хост системы 127.0.0.1:8080 - тут конечно имелось ввиду не публикация контейнера а публикация порта nginx в контейнере. мелочь, а неприятно


docker run -d --name "vshumskiy-custom-nginx-t2" -p 127.0.0.1:8080:80 barmaq/custom-nginx:1.0.0
docker ps

docker rename "vshumskiy-custom-nginx-t2" "custom-nginx-t2"

date +"%d-%m-%Y %T.%N %Z" ; sleep 0.150 ; docker ps ; ss -tlpn | grep 127.0.0.1:8080  ; docker logs custom-nginx-t2 -n1 ; docker exec -it custom-nginx-t2 base64 /usr/share/nginx/html/index.html
27-10-2024 10:39:54.049619603 UTC
CONTAINER ID   IMAGE                       COMMAND                  CREATED         STATUS         PORTS                    NAMES
83a314718757   barmaq/custom-nginx:1.0.0   "/docker-entrypoint.…"   5 minutes ago   Up 5 minutes   127.0.0.1:8080->80/tcp   custom-nginx-t2
LISTEN  0        4096           127.0.0.1:8080           0.0.0.0:*
172.17.0.1 - - [27/Oct/2024:10:34:45 +0000] "GET / HTTP/1.1" 200 95 "-" "curl/7.68.0" "-"
PGh0bWw+CjxoZWFkPgpIZXksIE5ldG9sb2d5CjwvaGVhZD4KPGJvZHk+CjxoMT5JIHdpbGwgYmUg
RGV2T3BzIEVuZ2luZWVyITwvaDE+CjwvYm9keT4KPC9odG1sPgo=

curl 127.0.0.1 8080

	Hey, Netology
	I will be DevOps Engineer!


--------------------------------------------------------
задание 3

docker attach custom-nginx-t2
cntrl+c
контейнер естественноп падает потому что мы останавливаем основной процесс. для выхода без остановки - Ctrl + P и Ctrl + Q
docker ps -a
docker start custom-nginx-t2
docker exec -it custom-nginx-t2 /bin/bash

apt-get update
apt-get install nano
nano /etc/nginx/conf.d/default.conf
меняем порт nginx 
nginx -s reload
curl http://127.0.0.1:80 ; curl http://127.0.0.1:81
exit

ss -tlpn | grep 127.0.0.1:8080
docker port custom-nginx-t2
curl http://127.0.0.1:8080
мы поменяли порт сервиса nginx в контейнере и текущая публикация порта никуда не ведет. 80й порт не слушается в контейнере

docker stop custom-nginx-t2
id контейнера 83a314718757
83a3147187575660f6e2cb619cb77c2d848a6d11cdcc67399c9abd968f7c6c4b

ls /var/lib/docker/containers/83a3147187575660f6e2cb619cb77c2d848a6d11cdcc67399c9abd968f7c6c4b
nano /var/lib/docker/containers/83a3147187575660f6e2cb619cb77c2d848a6d11cdcc67399c9abd968f7c6c4b

sudo nano /var/lib/docker/containers/83a3147187575660f6e2cb619cb77c2d848a6d11cdcc67399c9abd968f7c6c4b/hostconfig.json
sudo nano /var/lib/docker/containers/83a3147187575660f6e2cb619cb77c2d848a6d11cdcc67399c9abd968f7c6c4b/config.v2.json
systemctl restart docker
docker start custom-nginx-t2
curl http://127.0.0.1:8080
<html>
<head>
Hey, Netology
</head>
<body>
<h1>I will be DevOps Engineer!</h1>
</body>
</html>
--------------------------------------------------------
задание 4


docker run -d -t --name centos-test4 -v $(pwd):/data centos:latest
docker run -d -t --name debian-test4 -v $(pwd):/data debian:latest

docker exec -it centos-test4 bash
echo "hello im from centos container" > /data/centos.txt
exit
echo "hello im from dcker host" > host.txt
docker exec -it debian-test4 bash
ls /data

cat /data/centos.txt
cat /data/host.txt
----------------------------------------------------------
задача 5

mkdir -p /tmp/netology/docker/task5
cd /tmp/netology/docker/task5

nano compose.yaml
nano docker-compose.yaml
docker compose up -d
имя конфига по умолчанию compose.yaml. docker-compose.yaml поддерживается, но будет использван если не найдено compose.yaml или если файл конфига указан при запуске

registry Published ports are discarded when using host network mode 
у нас режим хост. поменял на дефолтный bridge , поскольку докер у меня на виртуалке

docker compose up -d

docker tag barmaq/custom-nginx:1.0.0 localhost:5000/custom-nginx:latest
docker push localhost:5000/custom-nginx:latest

настрйока портейнер
пароль xxxxxxxxxxxx ( заменен)

mv compose.yaml /tmp/netology/docker/compose.yaml
docker compose up -d
у контейнеров не было зависимости друг от друга, поэтому мы обнаужили контейнер сироту с portainer ( его компоуз файл был перемещен ). c registry все в порядке
чиним возвращая файл
mv /tmp/netology/docker/compose.yaml /tmp/netology/docker/task5/compose.yaml
docker compose down
