---
title: windows7搭建基于VIM的开发环境（2）- 编译64位且支持python的VIM版本并安装
categories: 开发环境搭建
tags: [vim,windows,x86_64]

---

> 本系列指导 主要是从无到有完整搭建windows下基于VIM的开发环境，主要分为以下几部分：

- **搭建msys2下64位编译环境**
- **编译64位且支持python的VIM版本并安装**<<font color=red>本篇</font>>
- **VIM基本配置**
- **VIM安装vundle**
- **VIM安装开发环境所需插件**

>本篇我们主要讲解编译64位且支持python和lua的VIM版本并安装。

#安装python
安装python到：D:\python\Python27,添加`D:\python\Python27;D:\python\Python27\Scripts`到PATH；
#安装Lua
从Lua官网下载`lua-5.3.3_Win64_dllw4_lib.zip`和`lua-5.3.3_Win64_bin.zip`，将`lua-5.3.3_Win64_dllw4_lib.zip`解压到`D:\lua`下，将`lua-5.3.3_Win64_bin.zip`解压到`D:\lua\bin`目录下，让后将两个目录都添加到PATH目录下即可。
> vim安装完后要将`D:\lua\lua53.dll`拷贝到与gvim.exe相同目录下。

# 下载VIM源码
从[ https://github.com/vim/vim ](https://github.com/vim/vim)下载vim源码，下载zip包比直接`git clone https://github.com/vim/vim.git`速度快一些。将下载的源码解压到$VIM_MASTER_PATH。

# 修改编译脚本
1.打开msys2 command,切换到$VIM_MASTER_PATH/src

```
cd $VIM_MASTER_PATH/src
```
2.复制Make_cyg_ming.mak编译脚本为current.mak

```
cp Make_cyg_ming.mak current.mak
```
3.编辑current.mak
查看`PYTHON_VER`和`PYTHON3_VER`是否与自己的python版本匹配，不匹配则修改为自己的python版本。
><font color=blue>注：需要提前安装python2和python3，例如安装目录分别为：`D:/python/Python27`和`D:/python/Python35`。</font>

在最上边增加：
搜索`ifdef PYTHON`，在`ifdef PYTHON`前增加`PYTHON=D:/python/Python27`

```
PYTHON=D:/python/Python27
PYTHON3=D:/python/Python35
LUA=D:/lua
```
# 编译VIM并打包

1.编译：

```
mingw32-make.exe -f current.mak
```
如果有编译失败，重新编译需要增加clean参数：

```
mingw32-make.exe -f custom.mak clean
```
编译成功后在src目录下会生成`gvim.exe `。

2.打包

切换到代码根目录
```
cd ..
```
创建运行脚本
```
vim package.sh
```
将以下内容复制到脚本中：

```
mkdir -p vimx64/vim
cp -a runtime/* vimx64/vim
cp -a src/*.exe vimx64/vim
cp -a src/GvimExt/gvimext.dll vimx64/vim
cp -a src/xxd/xxd.exe vimx64/vim
cp -a vimtutor.bat vimx64/vim
```
运行打包脚本
```
chmod +x package.sh
./package.sh
```
>至此，在`vimx64`下打包vim安装包。

3.安装
将vim安装包拷贝到待安装的目录，并更改vim文件夹名称为vim+版本号，例如我的安装目录为D:\vim，版本为8.0，则安装路径为：D:\vim\vim80。
以管理员身份运行install.exe，输入d并回车确认，安装成功后，会在D:\vim目录下生成vimfiles和_vimrc。