---
title: windows7搭建基于VIM的开发环境（1）- 搭建msys2下64位编译环境
categories: 开发环境搭建
tags: [vim,windows,x86_64]

---

> 本系列指导 主要是从无到有完整搭建windows下基于VIM的开发环境，主要分为以下几部分：

- **搭建msys2下64位编译环境**<<font color=red>本篇</font>>
- **编译64位且支持python的VIM版本并安装**
- **VIM基本配置**
- **VIM安装vundle**
- **VIM安装开发环境所需插件**

>本篇我们主要讲解msys2下64位编译环境的部署。

# 下载并安装msys2
从[ msys2官网 ](http://msys2.github.io/)下载64位msys2安装程序并安装到D:\msys64，新增环境变量：<font color=blue >MSYS2_HOME:D:\msys64</font>,在PATH中添加：<font color=blue >%MSYS2_HOME%\usr\bin;%MSYS2_HOME%\mingw64\bin</font>。


# 修改msys2软件源
1.修改<font color=blue >%MSYS2_HOME%\etc\pacman.d\mirrorlist.mingw32</font>
```
##
## 32-bit Mingw-w64 repository mirrorlist
## Changed on 2014-11-15
##
##北京理工大学镜像
Server = http://mirror.bit.edu.cn/msys2/REPOS/MINGW/i686
##日本北陆先端科学技术大学院大学 sourceforge 镜像
Server = http://jaist.dl.sourceforge.net/project/msys2/REPOS/MINGW/i686
##The UK Mirror Service Sourceforge mirror
Server = http://www.mirrorservice.org/sites/download.sourceforge.net/pub/sourceforge/m/ms/msys2/REPOS/MINGW/i686
## Primary
Server = ftp://148.251.42.38/MINGW/i686
## Sourceforge.net
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MINGW/i686
```
2.修改<font color=blue >%MSYS2_HOME%\etc\pacman.d\mirrorlist.mingw64</font>

```
##
## 64-bit Mingw-w64 repository mirrorlist
## Changed on 2014-11-15
##
##北京理工大学镜像
Server = http://mirror.bit.edu.cn/msys2/REPOS/MINGW/x86_64
##日本北陆先端科学技术大学院大学 sourceforge 镜像
Server = http://jaist.dl.sourceforge.net/project/msys2/REPOS/MINGW/x86_64
##The UK Mirror Service Sourceforge mirror
Server = http://www.mirrorservice.org/sites/download.sourceforge.net/pub/sourceforge/m/ms/msys2/REPOS/MINGW/x86_64
## Primary
Server = ftp://148.251.42.38/MINGW/x86_64
## Sourceforge.net
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MINGW/x86_64
```

3.修改<font color=blue >%MSYS2_HOME%\etc\pacman.d\mirrorlist.msys</font>

```
##
## MSYS2 repository mirrorlist
## Changed on 2014-11-15
##
##北京理工大学镜像
Server = http://mirror.bit.edu.cn/msys2/REPOS/MSYS2/$arch
##日本北陆先端科学技术大学院大学 sourceforge 镜像
Server = http://jaist.dl.sourceforge.net/project/msys2/REPOS/MSYS2/$arch
##The UK Mirror Service Sourceforge mirror
Server = http://www.mirrorservice.org/sites/download.sourceforge.net/pub/sourceforge/m/ms/msys2/REPOS/MSYS2/$arch
## Primary
Server = ftp://148.251.42.38/MSYS2/$arch
## Sourceforge.net
Server = http://downloads.sourceforge.net/project/msys2/REPOS/MSYS2/$arch
```



# 更新软件源并安装编译环境所需包

1.更新软件源：

```
pacman -Syu
```

2.更新msys2核心程序包

```
pacman -S --needed filesystem msys2-runtime bash libreadline libiconv libarchive libgpgme libcurl pacman ncurses libintl
```
*注：如果以上程序有更新，则需要重启bash。*

3.安装编译环境：

```
pacman -S mingw-w64-x86_64-gcc mingw-w64-x86_64-gdb mingw-w64-x86_64-make tmux zsh git  mingw64/mingw-w64-x86_64-cmake winpty diffutils
```
4.查看已安装软件包

```
pacman -Q --explicit
或者
pacman -Q -e
```
5.安装新软件包

```
pacman -S <package_names|package_groups>
```

6.删除软件包

```
pacman -R <package_names|package_groups>
```

7.搜索软件包

```
pacman -Ss <name_pattern>
```
8.列出所有软件组

```
pacman -Q --groups
```
8.验证环境
运行mingw64.exe，运行`gcc --version`,查看是否能显示出gcc版本信息：

```
gcc.exe (Rev2, Built by MSYS2 project) 6.2.0
Copyright (C) 2016 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

```
*注：可以将mingw64.exe锁定在任务栏