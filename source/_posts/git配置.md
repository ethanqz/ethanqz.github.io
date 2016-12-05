---
title: git配置
categories: 开发环境搭建
tags: [git,ssh,github]

---
# 配置用户名和Email
> 用户名和Email是用于代码提交日志标识。

```
git config --global user.name "name" 
git config --global user.email "mailaddress" 
```
# 配置git push
> 如果要使用git进行推送,则必须配置push.default ,否则推送失败。

```
git config --global push.default simple
```
# 在单独工程设置提交名称和email
```
git config  user.name "name" 
git config  user.email "mailaddress"
```
# 缓存https连接用户名和密码
linux
```
git config --global credential.helper  'cache --timeout 3600'
```
windows git bash
```
git config --global credential.helper store
```
> 注：<font color=red>msys2和termux都是使用的是windows的配置方法。</font>

# ssh key配置
## 在客户主机生成key
> 运行以下命令，会在~/.ssh/下生成id_rsa.pub文件。

```
ssh-keygen -t rsa 
```
## 将生成的公钥文件拷贝到目标服务器
```
scp ~/.ssh/id_rsa.pub user@ip:/home/user/new.pub
```
## 在被访问主机添加
> 上一步会将公钥文件拷贝到用户home目录下，查看是否存在new.pub，如果在~/.ssh目录下没有authorized_keys文件，则自己生成空文件，然后运行以下命令。

```
cat ~/.ssh/new.pub >> ~/.ssh/authorized_keys
```
## github添加ssh公钥
右上角个人控制台：setting->SSH and GPG keys->add NEW SSH，将id_rsa.pub文件的内容拷贝进去确认即可。