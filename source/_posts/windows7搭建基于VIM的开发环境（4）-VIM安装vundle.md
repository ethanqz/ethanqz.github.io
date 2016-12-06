---
title: windows7搭建基于VIM的开发环境（4）- VIM安装vundle
date: 2016-12-06 21:30:00
categories: 开发环境搭建
tags: [vim,windows,x86_64]

---

> 本系列指导 主要是从无到有完整搭建windows下基于VIM的开发环境，主要分为以下几部分：

- **搭建msys2下64位编译环境**
- **编译64位且支持python的VIM版本并安装**
- **VIM基本配置**
- **VIM安装vundle**<<font color=red>本篇</font>>
- **VIM安装开发环境所需插件**

>本篇我们主要讲解vim安装vundle，并使用vundle安装插件。

# 下载vundle
打开mingw64.exe，切换目录到vimfiles，并创建bundle目录:

```
cd /d/Vim/vimfiles/
mkdir bundle
cd bundle
```
从github更新vundle工程：

```
git clone https://github.com/gmarik/vundle.git
```


# 修改_vrmrc
新增vundle配置：

```
"--------------------------------------------------------------------------------
" vundle配置
"--------------------------------------------------------------------------------
filetype off  "vundle依赖filetype关闭
" 此处规定Vundle的路径  
set rtp+=$VIM/vimfiles/bundle/vundle/  
call vundle#rc('$VIM/vimfiles/bundle/')  
Bundle 'gmarik/vundle'  
filetype plugin indent on     " required! 
```
# 使用vundle安装插件
>在github上的插件, 如果是vim-scripts账户下的 插件 , 直接写;
>在github上的插件, 如果不是 vim-scripts账户下的插件, 写成 "账户名/插件名"
>安装：新增配置后，重新打开vim,在命令模式下输入命令PluginInstall。
>移除：删除插件配置后，重打开vim，在命令模式下输入命名PluginClean。

例如：NERDTree插件的安装：
```
"--------------------------------------------------------------------------------
" NERDTree
"--------------------------------------------------------------------------------
"NERDTree快捷键
Bundle 'scrooloose/nerdtree'
nmap <F2> :NERDTree  <CR>
" NERDTree.vim
let g:NERDTreeWinPos="left"
let g:NERDTreeWinSize=25
let g:NERDTreeShowLineNumbers=1
let g:neocomplcache_enable_at_startup = 1 
```