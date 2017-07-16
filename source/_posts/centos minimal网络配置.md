---
title: centos7 minimal网络配置
date: 2017-07-16 11:30:00
categories: linux
tags: [centos,linux]

---

# 配置当前为dhcp
  1.nmtui命令打开console图形配置界面，配置对应网卡为dhcp。
  2.service network restart重启网络生效。
  3.ip a查看。
  4.yum install net-tools使用ifconfig命令。

# 配置静态ip
  1.修改 BOOTPROTO=static
  2.增加配置
    ```
    IPADDR0=192.168.2.175
    PREFIX00=255.255.255.0
    GATEWAY0=192.168.2.1
    DNS1=8.8.8.8
    DNS2=8.8.4.4
    ```
  3.ip a查看。
  4.yum install net-tools使用ifconfig命令。