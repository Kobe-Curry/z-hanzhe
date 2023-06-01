## CentOS 7 安装Docker

官网地址：https://docs.docker.com/engine/install/centos

可选：卸载已安装的 Docker

```shell
$ yum remove docker \
    docker-client \
    docker-client-latest \
    docker-common \
    docker-latest \
    docker-latest-logrotate \
    docker-logrotate \
    docker-engine
```

下载 yum 工具包

```shell
$ yum install -y yum-utils
```

添加 Docker 源地址，这里换用阿里云的地址

```shell
$ yum-config-manager \
    --add-repo \
    http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

下载安装 Docker

```shell
$ yum -y install docker-ce docker-ce-cli containerd.io
```

```shell
# 启动docker服务
$ systemctl start docker
# 停止docker服务
$ systemctl stop docker
# docker开机自启
$ systemctl enable docker
```



## Ubuntu 22.04 安装Docker

官网地址：https://docs.docker.com/engine/install/ubuntu

可选-卸载已安装的 Docekr

```shell
$ sudo apt-get remove docker docker-engine docker.io containerd runc
```

更新并下载工具包

```shell
$ sudo apt-get update
$ sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

添加 Docker 的官方 GPG 密钥

```shell
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

设置存储库

```shell
$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

再次更新索引后安装 Docker

```shell
$ sudo apt-get update
$ sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```

```shell
# 启动docker服务
$ systemctl start docker
# 停止docker服务
$ systemctl stop docker
# docker开机自启
$ systemctl enable docker
```



## 辅助命令

```shell
# Docker版本信息查看
$ docker version

# Docker详细信息查看
$ docker info

# Docker帮助命令，可以在任何指令结尾使用--help查看帮助
$ docker [command] --help

# 查看Docker中某一项的详细信息
$ docker [command] inspect [id | name]
    [command]
        container：查看容器详情，在command中优先级最高
        image：查看镜像详情
        network：查看网桥详情
```



## 镜像命令

```shell
# 查看本机中所有镜像
$ docker images [options]
    [options]
        -a：列出所有镜像（包含中间映像层）
        -q：只显示镜像id

# 搜索镜像
$ docker search [options] [image]
    [options]
        -s：列出收藏数不少于指定值的镜像
        --no-trunc：显示完整的镜像信息

# 从仓库下载镜像，tag不填默认下载最新版
$ docker pull [image:tag]

# 删除镜像
$ docker rmi [options] [id | image]
    [options]
        -f：无视容器启动状态，强制删除镜像

# 11.打包镜像
$ docker save [id | image] -o 名称.tar

# 12.载入镜像
$ docker load -i 名称.tar
```



## 容器命令

当遇到容器与宿主机使用`:`关联时，除非明确指定哪一侧所在位置，其他默认使用 [ 宿主机:容器 ] 顺序

```shell
# 运行容器，未指定tag默认使用最新版，若本地没有最新版会自行下载
$ docker run [options] [image:tag]
    [options]
        --restart：容器重启策略
            no：容器退出时不重启容器
            always：容器推出时启动容器
            on-failure：指定退出n次后重启容器，例如 --restart on-failure:3
        --name：容器别名
        -d：启动守护式容器（在后台启动容器）
        -p：端口映射
        -P：随机指定宿主机端口映射
        -v：文件映射，也就是volume
        -e：用作向容器内传递初始化参数

# 创建容器，与run命令不同，create不会启动容器
$ docker create [options] [image:tag]
    [options]：可参考上面run参数，基本一致

# 查看容器列表，默认只查看运行中的容器
$ docker ps [options]
    [options]
        -a：显示所有容器
        -q：只显示容器ID

# 启动容器、重启容器、停止容器、强制停止容器、删除容器
$ docker start [id | container]
$ docker restart [id | container]
$ docker stop [id | container]
$ docker kill [id | container]
$ docker rm [options] [id | container]
    [options]
        -f：无视运行状态，强制删除容器

# 查看所有进程、暂停所有进程、恢复所有进程
$ docker top [id | container]
$ docker pause [id | container]
$ docker unpause [id | container]

# 查看容器的运行日志
$ docker logs [options] [id | container]
    [options]
        -t：加入时间戳
        -f：跟随最新的日志打印
        --tail：显示最后的几条日志
        --since：显示某个开始时间的所有日志，时间格式支持[RFC3339]、[年月日时分秒]、秒级时间戳

# 在容器中执行命令，options是执行参数，command是执行的目标
$ $ docker exec [options] [id | container] [command]
    [options]
        -i：以交互模式运行容器
        -t：分配一个伪终端
    [command]
        /bin/bash：最常用组合 -it /bin/bash，分配伪终端，以交互式运行bash，就可以执行容器内的命令了

# 容器和宿主机之间复制文件，如果[id | name]在左侧，就代表是从容器内复制文件到宿主机
$ docker cp [id | container]:[path] [path]
$ docker cp [container] [id | name]:[path]

# 容器打包成新的镜像
$ docker commit [options] [container:tag]
    [options]
        -a：镜像作者
        -c：使用Dockerfile指令来创建镜像
        -m：提交时的说明文字
        -p：在commit时，将容器暂停
```



## 网桥命令

```shell
# 查看网桥列表
$ docker network ls

# 创建网桥
$ docker network create [options] [network]
    [options]
        -d：驱动程序，就是ls中的driver列，默认使用bridge

# 删除网桥
$ docker network rm [network]

# 查看网桥详细信息以及使用网桥的容器
$ docker inspect network [network]
```



## Dockerfile

新建一个目录，目录内创建名为Dockerfile的文件，将与镜像相关的文件都放在该目录下

```shell
FROM         源镜像
MAINTAINER   作者信息

RUN          构建镜像时运行的指令
CMD          运行容器时执行的shell环境

VOLUME       指定容器挂载点到宿主机自动生成的目录或其他容器
WORKDIR      为 RUN、CMD、ENTRYPOINT、COPY 和 ADD 设置工作目录，就是切换目录

USER         为RUN、CMD、和 ENTRYPOINT 执行命令指定运行用户

HEALTHCHECH  健康检查

ARG          构建时指定的一些参数

EXPOSE       声明容器的服务端口（仅仅是声明）

ENV          设置容器环境变量

COPY         拷贝文件或目录到容器中
ADD          拷贝文件或目录到容器中，如果是URL会自动下载，压缩包会自动解压

ENTRYPOINT   运行容器时执行的shell命令
```