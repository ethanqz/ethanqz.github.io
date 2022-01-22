---
title: Ubuntu20.04根目录占满处理方法
date: 2022-01-22
description: Ubuntu20.04根目录占满后问题排查和处理方法
tags : [
    "Ubuntu",
]
categories : [
    "Linux",
]
toc : true
---
在使用Ubuntu Server20.04作为家用服务器时，由于磁盘未提前规划好，`/var`和`/home`目录未单独分空间，这两个目录增长过快导致根目录磁盘空间100%，系统无法正常运行，因此需要将这两个目录切换到增加的硬盘，保证系统正常运行。

<!--more--> 

![ubuntu图标](https://gitee.com/qz757/picgo/raw/master/img/ubuntu图标.jpg)

## 一、问题排查
### 1、排查整体目录和磁盘使用情况
首先通过`df -h`命令查看各目录使用情况，这里发现3个问题
- ubuntu--vg-ubuntu--lv挂载到根目录未占满使用的硬盘；
- 根目录使用率100%；
- 很多`/dev/loop`目录使用率100%；
*下图为查看示例，问题排查时未保留截图*
![](目录占用率查看.jpg)
### 2、查找具体问题目录
在根目录通过以下命令查找具体哪个目录过大导致根分区占满：
```
sudo du --max-depth=1 -h
```
![du查看每个目录占用](https://gitee.com/qz757/picgo/raw/master/img/du查看每个目录占用.png)
依次在有问题的目录通过该命令，最终定位到具体异常的目录，如果是需要删除的文件占用过大， 可直接删除解决，我当时排查到的两个问题：

- docker镜像占用的空间过大，导致`/var`目录增长过快，占满整个根分区；
- `/home`目录可用空间有限；
### 3、问题汇总
经过排查，需要解决的问题汇总如下：
- ubuntu--vg-ubuntu--lv挂载到根目录未占满使用的硬盘；
- 很多`/dev/loop`目录使用率100%；
- `/var`和`/home`目录增长过快，导致根分区占满；
## 二、问题解决
### 1、解决回环设备`/dev/loop`占用率100%问题

首先解释下为什么会出现这么多`/dev/loop`设备，其实这是正常现象，snap 使用的是 SquashFS 文件系统，这是一个只读的文件系统，所以它的大小在创建的时候一定是刚刚好能够存放它的内容就可以了，因为它是只读，所以它的大小之后不会改变。所以占用量肯定是 100%。
解决方法有两个：
- 如果不适用snap，直接卸载snap即可，命令如下
```shell
sudo apt autoremove --purge snapd
```

- 如果需要使用不能卸载，则可以屏蔽显示：
```shell
df -x squashfs -h
```

如果嫌弃每次输选项麻烦，可以在 "~/.bashrc" 文件里起别名：

```bash
echo "alias df='df -x squashfs -x tmpfs -x devtmpfs'" >> ~/.bashrc
```

然后 source 一下生效：

```bash
source ~/.bashrc
```
### 2、ubuntu--vg-ubuntu--lv未完整使用整个硬盘问题解决
命令`df /etc`可以查看`/etc`目所在的挂载路径磁盘使用情况：
```shell
Filesystem                        1K-blocks     Used Available Use% Mounted on
/dev/mapper/ubuntu--vg-ubuntu--lv 229114260 11471180 207131472   6% /
```
命令`vgdisplay`可以查看lvm卷组的信息；
如果发现ubuntu--vg-ubuntu--lv还可以扩容，则可以通过以下命令扩容：
```shell
sudo lvextend -L +100G /dev/mapper/ubuntu--vg-ubuntu--lv # 扩容100G
sudo resize2fs /dev/mapper/ubuntu--vg-ubuntu--lv # 生效扩容
sudo vgdisplay # 查看结果
```

其他扩容命令：
```shell
lvextend -L 10G /dev/mapper/ubuntu--vg-ubuntu--lv      //增大或减小至19G
lvextend -L +10G /dev/mapper/ubuntu--vg-ubuntu--lv     //增加10G
lvreduce -L -10G /dev/mapper/ubuntu--vg-ubuntu--lv     //减小10G
lvresize -l  +100%FREE /dev/mapper/ubuntu--vg-ubuntu--lv   //按百分比扩
```
### 3、将/var和/home目录挂载到新添加的磁盘
#### a、新添加磁盘分区
使用`df -T`命令可以查看已有文件系统格式；
```
fdisk -l # 可以查看所有磁盘列表以及各磁盘分区情况
fdisk /dev/sdb # 对需要的磁盘进行分区
m # 查询帮助
d # 删除已有分区
p # 显示已有分区
n # 新建分区
w # 写入分区结果
F # 显示为分配信息

## 通过n新建分区时，建议起始值使用建议值，不要从1或者紧接着上一个分区进行分配；

mkfs.ext4 /dev/sdb1 # 格式化新建的分区为ext4格式
```
分区结果如下：
![3T硬盘分区结果](https://gitee.com/qz757/picgo/raw/master/img/3T硬盘分区结果.png)

#### b、重新挂载目录
1. 创建临时目录 `sudo mkdir /storage`
2. 把`/dev/sdb1`挂载到`/storage`，命令：`sudo mount /dev/sdb1 /storage`
3. 拷贝数据到临时目录：`cp -pdr /var/* /storage/`
4. 备份原目录：`sudo mv /var /var_old`
5. 新建/var：`sudo mkdir /var`
6. 取消临时目录挂载：`sudo umount /dev/sdb1`
7. 重新挂载`/dev/sdb1`到`/var`：`mount /dev/sdb1 /var`
8. 清理对于目录：`rm -rf /var/lost+found`
9. 设置开机自动挂载磁盘:
```shell
vim /etc/fstab
## 增加卷自动挂载配置
UUID=9fb0bd07-50df-46e1-8944-db25f3ac100a /var   ext4 defaults 0 2 
UUID=c684f54c-057d-4e4f-91c0-c028837c2c00 /home  ext4 defaults 0 2

## 其中uuid可以通过以下命令查看
blkid /dev/sdb1
```
10. 重启服务器查看是否生效，生效后将old目录删除；

## 三、附录：linux目录说明

![Linux目录说明](https://gitee.com/qz757/picgo/raw/master/img/Linux目录说明.png)

## 标签和链接
Index： #Linux-Index 
Info： #Ubuntu 

## 参考文献
[/etc/fstab详细参数配置](https://www.cnblogs.com/jianglaoda/p/9253282.html)