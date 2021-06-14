[![Build Status](https://travis-ci.com/qt-legion/lab08.svg?branch=main)](https://travis-ci.com/qt-legion/lab08)
```sh
$ export GITHUB_USERNAME=qt-legion
```

```
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab777 lab08
$ cd lab08
$ git submodule update --init
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab08
```

```sh
$ cat > Dockerfile <<EOF
FROM ubuntu:18.04
EOF
```

```sh
$ cat >> Dockerfile <<EOF

RUN apt update
RUN apt install -yy gcc g++ cmake
EOF
```

```sh
$ cat >> Dockerfile <<EOF

COPY . print/
WORKDIR print
EOF
```
В докере делаем cmake
```sh
$ cat >> Dockerfile <<EOF

RUN cmake -H. -B_build -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=_install
RUN cmake --build _build
RUN cmake --build _build --target install
EOF
```

```sh
$ cat >> Dockerfile <<EOF

ENV LOG_PATH /home/logs/log.txt
EOF
```

```sh
$ cat >> Dockerfile <<EOF

VOLUME /home/logs
EOF
```
переходим в директорию
```sh
$ cat >> Dockerfile <<EOF

WORKDIR _install/bin
EOF
```
Создаём точку входа в приложение
```sh
$ cat >> Dockerfile <<EOF

ENTRYPOINT ./demo
EOF
```

```sh
$ sudo docker build -t logger .
```
Просим докер показать нам, какие существующие образы у нас есть
```sh
$ docker images
```
создаём директорию logs, создаём интеррактивный процесс logger и записываем туда текст
```sh
$ mkdir logs
$ docker run -it -v "$(pwd)/logs/:/home/logs/" logger
text1
text2
text3
<C-D>
```
Просим докер вывести подробную информацию о контейнере
```sh
$ docker inspect logger
```
Проверяем сохранены ли файлы
```sh
$ cat logs/log.txt
```

```sh
$ gsed -i 's/lab07/lab08/g' README.md
```
Редактируем travis.yml
```sh
$ vim .travis.yml
/lang<CR>o
services:
- docker<ESC>
jVGdo
script:
- docker build -t logger .<ESC>
:wq
```
Добавляем всё это чудо на гитхаб
```sh
$ git add Dockerfile
$ git add .travis.yml
$ git commit -m"adding Dockerfile"
$ git push origin master
```
Логинимся в трэвис и запускаем его
```sh
$ travis login --token
$ travis enable
```
