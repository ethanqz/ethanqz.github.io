---
title: 使用Docker部署Scratch3.0
date: 2022-01-22
description: 使用Docker部署Scratch3.0
tags : [
    "Scratch",
]
categories : [
    "Linux",
]
toc : true
---
Scratch是由MIT（麻省理工学院）米切尔·瑞斯尼克(Mitch Resnick)教授带领的“终身幼儿园团队”(Lifelong Kindergarten Group)开发的一款积木式少儿编程软件。

通过类似拖动积木块的方式和使用软件中的素材，可以很容易地创造有趣的动画、游戏，以及控制机器人和电子设备等，在创作的过程中不仅习得了Science（科学）, Technology（技术）, Engineering（工程）, Arts（艺术）, Maths（数学）等多个领域的知识，而且可以培养逻辑思维能力、观察能力、创新能力和想象力。

<!--more--> 

![scratch3.0图标](https://gitee.com/qz757/picgo/raw/master/img/scratch3.0图标.jpeg)

## 简介
想在家里的服务器部署Scratch3.0环境给小朋友学编程，但是pad安装安卓版本后现实很小，就想部署服务器版本通过浏览器操作，因此在ubuntu使用docker部署容器，基于Ubuntu部署Docker环境可参考[基于Ubuntu部署Docker环境](../基于ubuntu部署docker环境)。

整体流程如下：本地下载Scratch3.0源码->使用node:12.16镜像进行npm编译->使用apache镜像发布；
## 一、Scratch3.0源码下载

下载scratch-gui、scratch-blocks、scratch-www、scratch-vm四个仓库的代码到本地，命令如下：

```shell
git clone git@github.com:LLK/scratch-gui.git
git clone git@github.com:LLK/scratch-blocks.git
git clone git@github.com:LLK/scratch-www.git
git clone git@github.com:LLK/scratch-vm.git
```

## 二、使用node:12.16镜像进行编译

### 1、启动容器
使用以下命令进入容器shell，node镜像接近1G，如果下载慢，可以参考[ubuntu环境使用Docker部署服务](ubuntu环境使用Docker部署服务-202201081127.md)配置镜像加速器，也可以使用node:12.16-alpine3.11镜像，这个小一些，按时alpine不熟悉，因此我还是使用基于debian的node镜像，同时将代码目录挂载到容器中：

```shell
docker run -ti -v ~/work/data/scratch:/scratch node:12.16 bash
```

### 2、配置编译环境

```shell
## 修改镜像源为阿里云
sed -i s@/deb.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list 
sed -i s@/security.debian.org/@/mirrors.aliyun.com/@g /etc/apt/sources.list
## 更新
apt update 
apt upgrade -y
## 安装vim
apt install vim 
vim /etc/apt/sources.list
```
将整个软件源替换为阿里源(debian11.x)：
```
deb http://mirrors.aliyun.com/debian/ bullseye main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye main non-free contrib
deb http://mirrors.aliyun.com/debian-security/ bullseye-security main
deb-src http://mirrors.aliyun.com/debian-security/ bullseye-security main
deb http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye-updates main non-free contrib
deb http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ bullseye-backports main non-free contrib
```
然后再更新一次，并安装必要的依赖包：
```shell
## 更新
apt update 
apt upgrade -y
## 安装依赖包
apt install git  python2 curl openssh-server openssh-client
```

### 3、安装npm依赖包
```shell
## 配置阿里源并查看
npm config set registry https://registry.npm.taobao.org/
npm config get registry

npm i -g webpack webpack-cli webpack-dev-server
cd /scratch/scratch-gui/
npm install
BUILD_MODE=dist npm run build
```
在build目录下会生成编译好的网站文件；
## 三、使用apache镜像发布
```shell
## 将编译生成的buid目录挂载到apache2的默认网站路径
docker run -d --name apache2-container -e TZ=UTC -p 8181:80 -v ~/work/data/scratch/scratch-gui/build:/var/www/html ubuntu/apache2
```

## 四、使用已有Scratch3.0镜像直接发布
```shell
docker run -d -p 7777:80 xaviblanes/scratch3.0 /bin/bash -c "nohup ping -i 1000 www.baidu.com"
docker exec -it 'container id'
service apache2 start
```
## 标签和链接
Index： #Linux-Index 

Info： #Scratch

## 参考文献
[使用 Docker 轻松构建 Scratch 3.0](https://qiita.com/ken992/items/d515cad8f3f5ce206d01)
[CentOS(宝塔)部署安装发布Scratch3.0](https://blog.csdn.net/baidu_32523857/article/details/105104009)