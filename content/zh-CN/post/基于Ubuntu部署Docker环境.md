---
title: 基于Ubuntu部署Docker环境
date: 2022-01-21
description: 使用Docker配置Docker环境
tags : [
    "Docker",
]
categories : [
    "Linux",
]
toc : true
---
在看了[ Dock Life: Using Docker for All The Things!](https://nystudio107.com/blog/dock-life-using-docker-for-all-the-things)这篇文章后，解决了我一直以来使用Linux的一个担忧，由于对Linux运维并没有非常熟悉，因此有时候安装了软件失败或者卸载后，有文件残留，因此希望能与host进行隔离，Docker便成了最好的解决方案。如果使用的镜像多，建议将`/var`目录独立分区，镜像下载到/var目录下，避免过度导致根目录被占满，如果有类似问题可参考：[Ubuntu20.04根目录占满处理方法](../ubuntu20.04根目录占满处理方法)。

<!--more--> 

![Docker图标1](https://gitee.com/qz757/picgo/raw/master/img/Docker图标1.jpg)

## 一、网络配置

未进行特殊配置，直接在路由器手动绑定mac和ip，系统内部仍然使用dhcp；

## 二、修改阿里源

### 1、通过命令行修改：
执行以下`sed`命令替换`/etc/apt/sources.list`中的所有地址：
```
sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list
```

### 2、通过文件修改：

这是[阿里的镜像地址](https://developer.aliyun.com/mirror/)，可以查看各种系统的镜像源，ubuntu修改`/etc/apt/sources.list`文件如下：

```shell
deb http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-security main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-updates main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-proposed main restricted universe multiverse

deb http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ focal-backports main restricted universe multiverse
```

## 三、系统更新

```shell
sudo apt-get -y update
sudo apt-get -y upgrade
```

## 四、安装docker

### 1、安装docker并配置镜像加速器

在[阿里容器镜像服务](https://cr.console.aliyun.com/)注册开发者账号，我的账号是`qz757`邮箱是`qz757@sina.com`，登录后，在[阿里加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)获取专属加速器地址，类似这样`https://xxxxxx.mirror.aliyuncs.com`，然后按照[指导](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)进行配置；

- 安装docker

```shell
# step 1: 安装必要的一些系统工具
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# step 2: 安装GPG证书
curl -fsSL http://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# Step 3: 写入软件源信息
sudo add-apt-repository "deb [arch=amd64] http://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# Step 4: 更新并安装 Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce
```

- 配置镜像加速器

针对Docker客户端版本大于 1.10.0 的用户，可以通过修改daemon配置文件/etc/docker/daemon.json来使用加速器：

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://xxxxx.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

### 2、配置当前用户权限

如果希望当前用户可运行docker，则将当前用户加到docker组：

```shell
sudo groupadd docker # 安装完docker应该默认存在了，提示已存在说明已默认创建好
sudo gpasswd -a $USER docker
newgrp docker
```

### 3、docker命令

docker命令可以参考：[docker教程](https://www.runoob.com/docker/docker-tutorial.html)

## 五、Docker常用命令

```shell
$ docker ps // 查看所有正在运行容器
$ docker stop containerId // containerId 是容器的ID

$ docker ps -a // 查看所有容器
$ docker ps -a -q // 查看所有容器ID

$ docker rm [container id] //删除容器

$ docker images //查看所有镜像

$ docker rmi [image id] //删除镜像

$ docker stop $(docker ps -a -q) //  stop停止所有容器
$ docker  rm $(docker ps -a -q) //   remove删除所有容器
$ docker rm `docker ps -a|grep Exited|awk '{print $1}'` //删除所有Exited状态的容器

$ docker logs -tf --tail="50" container_name //查看已启动容器日志
$ docker exec -it container_name bash/sh //进入已启动容器shell
$ docker run -ti --rm -v ~/tmp:/tmpdata qz757/ubuntu-python:1.0.0 bash //以bash方式启动容器并挂载临时目录给容器

$ docker commit [container id] [image name]
```

## 六、使用Makefile+Dockerfile制作Docker镜像

以基于ubuntu镜像构建包含python、git包的调测镜像为例：

### 1、安装make和make-doc
```shell
sudo apt-get install make-doc make -y
```

2、修改Dockerfile
```Dockerfile
FROM ubuntu
RUN sed -i s@/archive.ubuntu.com/@/mirrors.aliyun.com/@g /etc/apt/sources.list \
    && apt-get clean \
    && apt-get update \
    && apt-get install --assume-yes apt-utils sudo dialog \
    && echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections \
    && apt-get upgrade -y \
    && apt-get install python3 python3-pip python-is-python3 vim wget curl git -y \
    && apt-get upgrade -y \
    && pip install pip -U \
    && pip config set global.index-url https://mirrors.aliyun.com/pypi/simple/
```

制作镜像时给容器内部配置代理：

```shell
ENV http_proxy http://192.168.2.169:7890

ENV https_proxy http://192.168.2.169:7890
```

### 3、修改Makefile
```shell
NAME = qz757/ubuntu-python
VERSION = 1.0.0

.PHONY: build start push

build:  build-version

build-version:
        docker build -t ${NAME}:${VERSION}  .

tag-latest:
        docker tag ${NAME}:${VERSION} ${NAME}:latest

start:
        docker run -it --rm ${NAME}:${VERSION} /bin/bash

push:   build-version tag-latest
        docker push ${NAME}:${VERSION}; docker push ${NAME}:latest
```

### 4、制作并上传镜像
- 首先通过`docker login`命令登录到docker hub；
- 修改Makefile名称和版本号，qz757是账号，必须和登录的账号一致，否则无法提交；
- `make build`构建容器；
- `make start`调测镜像，完成后容器会删除；
- `make push`给镜像打lastest标签，然后提交到docker hub；

## 七、安装docker-compose

- 从github下载docker-compose文件下载到`/usr/local/bin/`下（当前该目录下为空，未安装任何其他可执行文件）：

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
# 要安装其他版本的 Compose，请替换 v2.2.2
```

- 将可执行权限应用于二进制文件：

```shell
$ sudo chmod +x /usr/local/bin/docker-compose
```

- ~~创建软链（可选，ubuntu可执行/usr/local/bin文件）：~~

```shell
$ sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
```

- 测试是否安装成功：

```shell
$ docker-compose --version
Docker Compose version v2.2.2
```

> **注意**： 对于 alpine，需要以下依赖包： py-pip，python-dev，libffi-dev，openssl-dev，gcc，libc-dev，和 make。

## 八、Docker  Compose常用命令

```shell
$ docker-compose -f xx.yml up -d // 后台启动容器
$ docker-compose -f xx.yml down // 停止并删除容器
$ docker-compose -f xx.yml stop // 停止容器
$ docker-compose -f xx.yml start // 启动容器
```

## 标签和链接
Index： #Linux-Index 
Info： #Docker 

## 参考文献

[Dock Life: Using Docker for All The Things!](https://nystudio107.com/blog/dock-life-using-docker-for-all-the-things)