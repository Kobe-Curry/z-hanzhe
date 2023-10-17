这里整理一些我个人常用环境的部署命令，随用随补节省部署时间，命令涉及到`:`绑定默认左侧物理机右侧容器

```shell
# 个人习惯将容器都放在网桥上，在创建容器之前应该先创建网桥
$ docker network create mynet

# 后面启动容器时可以通过 --network 指定容器所属网桥
$ docker run --network mynet  ....
```

容器可能面对时区问题，可在创建容器时通过环境变量指定时区，如果无效就使用物理机的时区文件覆盖掉容器内的时区文件

```shell
# 环境变量指定时区
$ docker run -e TZ=Asia/Shanghai ...

# CentOS发行版的命令
$ docker run -v /usr/share/zoneinfo/Asia/Shanghai:/etc/localtime:ro ...

#Ubuntu发行版的命令
......
```



## MySQL

```shell
# 下载镜像
$ docker pull mysql:5.7.41

# 初始化目录
$ mkdir -p /opt/docker/mysql
$ cd /opt/docker/mysql

# 裸启动
$ docker run --name mysql -e MYSQL_ROOT_PASSWORD=mysql -d mysql:5.7.41

# 拷贝文件
$ docker cp mysql:/etc/mysql /opt/docker/mysql/
$ mv mysql conf
$ docker cp mysql:/var/lib/mysql /opt/docker/mysql/
$ mv mysql data

# 删除后重启容器
$ docker rm -f mysql
$ docker run --name mysql \
   --restart always \
   -p 3306:3306 \
   -e TZ=Asia/Shanghai \
   -v /opt/docker/mysql/data:/var/lib/mysql \
   -v /opt/docker/mysql/logs:/var/log/mysql \
   -v /opt/docker/mysql/conf:/etc/mysql \
   -d mysql:5.7.41
```

这里补充一个可能会用到的操作，例如我使用的是国外的服务器，国内访问延迟较高备份较慢，因为Navicat是依次获取每张表的表结构和数据，延迟较高时会影响效率，可以直接在远程服务器中使用`dump`命令将数据库转存为SQL文件，然后使用FPT工具将SQL文件下载下来即可，这里记录一下操作命令

```shell
# 进入到MySQL容器中
$ docker exec -it mysql /bin/bash

# 将指定库导出SQL到指定位置，推荐`/var/lib/mysql/`目录下，因为与宿主机做了目录映射可以直接下载
$ mysqldump -u [用户名] -p [数据库名] > /var/lib/mysql/[文件名].sql
```



## Redis

Redis镜像中并没有提供配置文件，要想自定义配置需要手动[下载配置文件](https://gitee.com/mirrors/redis/blob/5.0.10/redis.conf)，然后上传到服务器

```shell
# 在执行命令前请先将配置文件上传至服务器
# 注意修改配置文件中的bind与requirepass
# 设置bind为0.0.0.0为允许外网访问，requirepass为访问密码

# 下载镜像
$ docker pull redis:5.0.10

# 初始化目录，移动配置文件
$ mkdir -p /opt/docker/redis/conf
$ mv redis.conf /opt/docker/redis/conf/redis.conf

# 启动容器
$ docker run --name redis \
   --restart always \
   -p 6379:6379 \
   -e TZ=Asia/Shanghai \
   -v /opt/docker/redis/data:/data \
   -v /opt/docker/redis/conf:/opt/conf \
   -d redis:5.0.10 redis-server /opt/conf/redis.conf
```



## Nginx

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
   --restart always \
   -p 80:80 \
   -p 443:443 \
   -e TZ=Asia/Shanghai \
   -v /opt/docker/nginx/conf/:/etc/nginx \
   -v /opt/docker/nginx/webs/:/usr/share/nginx \
   -v /opt/docker/nginx/logs/:/var/log/nginx \
   -v /opt/docker/nginx/ssl/:/ssl \
   -d nginx:1.22.0
```



## PHP-FPM

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
   --restart always \
   -p 9000:9000 \
   -e TZ=Asia/Shanghai \
   -v /opt/docker/nginx/webs:/var/www/html \
   -v /opt/docker/php/conf:/usr/local/etc/php \
   php:7.2-fpm
```



## Nacos

> 单机模式鉴权启动

```shell
# 下载镜像
$ docker pull nacos-server:v2.2.3

# 初始化目录
$ mkdir -p /opt/docker/nacos

# 裸启容器
$ docker run --name nacos \
   -p 8848:8848 \
   -e MODE=standalone \
   -e JVM_XMS=256m \
   -e JVM_XMX=256m \
   -d nacos/nacos-server:v2.2.3

# 拷贝配置文件
$ docker cp nacos:/home/nacos/conf /opt/docker/nacos/conf
$ docker cp nacos:/home/nacos/data /opt/docker/nacos/data
$ docker cp nacos:/home/nacos/logs /opt/docker/nacos/logs

# 删除裸启的容器
$ docker rm -f nacos

# 正式启动容器，访问地址为http://ip:8848/nacos
$ docker run --name nacos \
   --restart always \
   -p 8848:8848 \
   -p 9848:9848 \
   -e MODE=standalone \
   -e JVM_XMS=256m \
   -e JVM_XMX=256m \
   -v /opt/docker/nacos/conf:/home/nacos/conf \
   -v /opt/docker/nacos/data:/home/nacos/data \
   -v /opt/docker/nacos/logs:/home/nacos/logs \
   -d nacos/nacos-server:v2.2.3
```

Nacos启动后默认没有权限校验，所有人可访问操作，可以编辑`conf`目录下的`application.properties`配置文件，在底部添加配置即可实现鉴权，具体参考官方文档：https://nacos.io/zh-cn/docs/v2/guide/user/auth.html

```properties
# 开启Nacos鉴权
nacos.core.auth.enabled=true
nacos.core.auth.system.type=nacos
nacos.core.auth.plugin.nacos.token.secret.key=Token密钥要求Base64编码后长度超过32位
nacos.core.auth.server.identity.key=
nacos.core.auth.server.identity.value=
```



## Minio

高版本的Minio将管理地址和API请求地址拆分开了，需要在运行容器的时候指定端口号才可以使用-p绑定

console-address为管理地址，address为API请求地址address，如不指定容器会随机分配不便处理

```shell
# 下载镜像
$ docker pull minio/minio:latest

# 启动容器
$ docker run --name minio \
   --restart always \
   -p 9000:9000 \
   -p 9001:9001 \
   -e TZ=Asia/Shanghai \
   -e MINIO_ACCESS_KEY=admin \
   -e MINIO_SECRET_KEY=123456789 \
   -v /opt/docker/minio/data:/data \
   -v /opt/docker/minio/conf:/root/.minio \
   -d minio/minio:latest server /data --console-address ":9000" --address ":9001"
```



## Typecho

```shell
# 下载镜像
$ docker pull 80x86/typecho:v1.2.1-rc.2-amd64

# 裸启容器
$ docker run --name typecho \
   -p 80:80 \
   -e PHP_TZ=Asia/Shanghai \
   -e PHP_MAX_EXECUTION_TIME=600 \
   -d 80x86/typecho:v1.2.1-rc.2-amd64

# 拷贝数据
$ docker cp typecho:/app /opt/docker/typecho

# 删除裸启的容器
$ docker rm -f typecho

# 启动容器
$ docker run --name typecho \
   --restart always \
   -p 80:80 \
   -e PHP_TZ=Asia/Shanghai \
   -e PHP_MAX_EXECUTION_TIME=600 \
   -v /opt/docker/typecho:/data \
   -d 80x86/typecho:v1.2.1-rc.2-amd64
```



## 青龙面板

```shell
# 下载镜像
$ docker pull whyour/qinglong:latest

# 启动容器
$ docker run --name ql \
   --restart always \
   -p 5700:5700 \
   -v /opt/docker/ql/config:/ql/config \
   -v /opt/docker/ql/log:/ql/log \
   -v /opt/docker/ql/db:/ql/db \
   -d whyour/qinglong:latest
```



## Baiduwp

```shell
# 下载镜像
$ docker pull yuantuo666/baiduwp-php:3.0.2

# 启动容器
$ docker run --name baiduwp \
   --restart always \
   -p 80:80 \
   -d yuantuo666/baiduwp-php:3.0.2
```



## ChatGPT-Next-Web

```shell
# 下载镜像
$ docker pull yidadaa/chatgpt-next-web:2.9.5

# 启动容器
$ docker run --name chatgpt-web \
   --restart always \
   -p 3000:3000 \
   -e OPENAI_API_KEY=OpenAI-KEY \
   -e CODE=Password \
   -d yidadaa/chatgpt-next-web:2.9.5
```
