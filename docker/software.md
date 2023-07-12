这里收集一些常用环境的安装步骤，可无脑复制命令逐行执行（注意网桥），节省安装时间，随时用到随时补充

命令涉及到`:`绑定相关命令，默认左侧为物理机，右侧为容器

```shell
# 个人习惯将容器都放在网桥上，在创建容器之前应该先创建网桥
$ docker network create channel
```

docker容器可能会面对时区问题，可以在创建容器的时候用-e指定时区，如果无效就使用-v命令将物理机的时区文件覆盖掉容器内的时区文件

```
# CentOS发行版的命令
-v /usr/share/zoneinfo/Asia/Shanghai:/etc/localtime:ro

#Ubuntu发行版的命令 待补充
```


## MySQL

> MySQL 5.7.41

```shell
# 下载镜像
$ docker pull mysql:5.7.41

# 初始化目录
$ mkdir -p /opt/docker/mysql
$ cd /opt/docker/mysql

# 裸启动
$ docker run --name mysql -e MYSQL_ROOT_PASSWORD=123456 -d mysql:5.7.41

# 拷贝文件
$ docker cp mysql:/etc/mysql /opt/docker/mysql/
$ mv mysql conf
$ docker cp mysql:/var/lib/mysql /opt/docker/mysql/
$ mv mysql data

# 删除后重启容器
$ docker rm -f mysql
$ docker run --name mysql \
   -e TZ=Asia/Shanghai \
   -p 3306:3306 \
   --restart always \
   --network channel \
   -v /opt/docker/mysql/data:/var/lib/mysql \
   -v /opt/docker/mysql/logs:/var/log/mysql \
   -v /opt/docker/mysql/conf:/etc/mysql \
   -e MYSQL_ROOT_PASSWORD=123456 \
   -d mysql:5.7.41
```



## Redis

Redis镜像中并没有提供配置文件，要想自定义配置需要手动[下载配置文件](https://gitee.com/mirrors/redis/blob/5.0.10/redis.conf)，然后上传到服务器

> Redis 5.0.10

```shell
# 在执行命令前请先将配置文件上传至服务器
# 注意修改配置文件中的bind与requirepass
# 设置bind为0.0.0.0为允许外网访问，requirepass为访问密码

# 下载镜像
$ docker pull redis:5.0.10

# 初始化目录，移动配置文件
$ mkdir -p /opt/docker/redis/conf
$ mv redis.conf /opt/docker/redis/conf/redis.conf
$ cd /opt/docker/redis

# 启动容器
$ docker run --name redis \
   -e TZ=Asia/Shanghai \
   -p 6379:6379 \
   --restart always \
   --network channel \
   -v /opt/docker/redis/data:/data \
   -v /opt/docker/redis/conf:/opt/conf \
   -d redis:5.0.10 redis-server /opt/conf/redis.conf
```



## Nginx

> nginx 1.22.0

```shell
# 下载镜像
$ docker pull nginx:1.22.0

# 初始化目录
$ mkdir -p /opt/docker/nginx/ssl
$ cd /opt/docker/nginx

# 裸启动
$ docker run -d --name nginx nginx:1.22.0

# 拷贝文件
$ docker cp nginx:/etc/nginx /opt/docker/nginx
$ mv nginx conf
$ docker cp nginx:/usr/share/nginx /opt/docker/nginx
$ mv nginx webs

# 删除裸启的容器
$ docker rm -f nginx

# 正式启动容器
$ docker run --name nginx \
   -e TZ=Asia/Shanghai \
   -p 80:80 \
   -p 443:443 \
   --restart always \
   --network channel \
   -v /opt/docker/nginx/conf/:/etc/nginx \
   -v /opt/docker/nginx/webs/:/usr/share/nginx \
   -v /opt/docker/nginx/logs/:/var/log/nginx \
   -v /opt/docker/nginx/ssl/:/ssl \
   -d nginx:1.22.0
```



## Minio

版本较高的Minio将管理地址和API请求地址拆分开了，需要在运行容器的时候指定端口号才可以使用-p绑定

console-address为管理地址，address为API请求地址address，如不指定容器会随机分配不便处理

> minio RELEASE.2022-11-17T23-20-09Z

```shell
# 下载镜像
$ docker pull minio/minio:latest

# 启动容器
$ docker run --name minio \
   -e TZ=Asia/Shanghai \
   -p 9000:9000 \
   -p 9001:9001 \
   --restart always \
   --network channel \
   -e MINIO_ACCESS_KEY=admin \
   -e MINIO_SECRET_KEY=123123123 \
   -v /opt/docker/minio/data:/data \
   -v /opt/docker/minio/conf:/root/.minio \
   -d minio/minio server /data --console-address ":9000" --address ":9001"
```



## PHP

> PHP7

```shell
# 下载镜像
$ docker pull php:7.2-fpm

# 初始化目录
$ mkdir -p /opt/docker/php

# 裸启容器
$ docker run -d --name php php:7.2-fpm

# 拷贝配置文件
$ docker cp php:/usr/local/etc/php /opt/docker/php/
$ mv /opt/docker/php/php/ /opt/docker/php/conf

# 删除裸启的容器
$ docker rm -f php

# 正式启动容器
$ docker run -d --name php \
   -e TZ=Asia/Shanghai \
   --restart always \
   --network channel \
   -v /opt/docker/nginx/webs:/var/www/html \
   -v /opt/docker/php/conf:/usr/local/etc/php \
   php:7.2-fpm
```