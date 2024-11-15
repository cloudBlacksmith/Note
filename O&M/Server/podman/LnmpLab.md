##### 前言
我一直认为"实践出真知"最好的学习方法就是过一次教程,然后去做一个作品;
本文目的为将在nginx目录下部署过的项目容器化部署; 
因为我们要部署的项目并不是专门的容器化项目,所以我们需要更改的位置较多;

##### 镜像准备
由于 unit 提供的官方镜像 `unit:php8.3` 以及 php 提供的官方镜像中并没有 `gd` 和 `mysqli` 扩展,所以一不做二不休直接全部自定义images;

##### Php镜像
```Dockerfile
FROM docker.io/library/php:8.3.12RC1-fpm-alpine

RUN apk update
RUN apk add zlib-dev libpng-dev libjpeg-turbo-dev freetype-dev

RUN docker-php-ext-configure gd --with-freetype --with-jpeg
RUN docker-php-ext-install gd mysqli

CMD [ "php-fpm" ] 
```

##### Unit镜像
```Dockerfile

```
