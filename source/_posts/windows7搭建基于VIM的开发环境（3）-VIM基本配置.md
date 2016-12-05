---
title: windows7搭建基于VIM的开发环境（3）- VIM基本配置
categories: 开发环境搭建
tags: [vim,windows,x86_64]

---


> 本系列指导 主要是从无到有完整搭建windows下基于VIM的开发环境，主要分为以下几部分：

- **搭建msys2下64位编译环境**
- **编译64位且支持python的VIM版本并安装**
- **VIM基本配置**<<font color=red>本篇</font>>
- **VIM安装vundle**
- **VIM安装开发环境所需插件**

>本篇我们主要讲解VIM的基本配置，在[上一篇](http://qz757.github.io/2016/12/04/windows7%E6%90%AD%E5%BB%BA%E5%9F%BA%E4%BA%8EVIM%E7%9A%84%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%EF%BC%882%EF%BC%89-%E7%BC%96%E8%AF%9164%E4%BD%8D%E4%B8%94%E6%94%AF%E6%8C%81python%E7%9A%84VIM%E7%89%88%E6%9C%AC%E5%B9%B6%E5%AE%89%E8%A3%85/)我们已经完成了VIM的安装，本篇的配置主要是修改上篇安装生成的_vrmrc。

# 安装中文vimdoc
从[ http://vimcdoc.sourceforge.net/](http://vimcdoc.sourceforge.net/)下载最新中文版vimdoc并安装。安装时vim安装目录要选择到`D:\Vim\vim80\`。


# 同步官方exe默认配置
安装完成后默认配置为：

```
source $VIMRUNTIME/vimrc_example.vim
source $VIMRUNTIME/mswin.vim
behave mswin

```
增加diff配置，此为官方exe安装版diff配置，默认添加即可，未验证具体功能。修改后为：

```
"--------------------------------------------------------------------------------
" 默认
"--------------------------------------------------------------------------------
source $VIMRUNTIME/vimrc_example.vim
source $VIMRUNTIME/mswin.vim
behave mswin

set diffexpr=MyDiff()
function MyDiff()
  let opt = '-a --binary '
  if &diffopt =~ 'icase' | let opt = opt . '-i ' | endif
  if &diffopt =~ 'iwhite' | let opt = opt . '-b ' | endif
  let arg1 = v:fname_in
  if arg1 =~ ' ' | let arg1 = '"' . arg1 . '"' | endif
  let arg2 = v:fname_new
  if arg2 =~ ' ' | let arg2 = '"' . arg2 . '"' | endif
  let arg3 = v:fname_out
  if arg3 =~ ' ' | let arg3 = '"' . arg3 . '"' | endif
  if $VIMRUNTIME =~ ' '
    if &sh =~ '\<cmd'
      if empty(&shellxquote)
        let l:shxq_sav = ''
        set shellxquote&
      endif
      let cmd = '"' . $VIMRUNTIME . '\diff"'
    else
      let cmd = substitute($VIMRUNTIME, ' ', '" ', '') . '\diff"'
    endif
  else
    let cmd = $VIMRUNTIME . '\diff'
  endif
  silent execute '!' . cmd . ' ' . opt . arg1 . ' ' . arg2 . ' > ' . arg3
  if exists('l:shxq_sav')
    let &shellxquote=l:shxq_sav
  endif
endfunction
```


# 增加中文支持

```
"--------------------------------------------------------------------------------
" 配置多语言环境,解决中文乱码问题
"--------------------------------------------------------------------------------
if has("multi_byte") 
    " UTF-8 编码 
    set encoding=utf-8 
    set termencoding=utf-8 
    set formatoptions+=mM 
    set fencs=utf-8,gbk 
    if v:lang =~? '^/(zh/)/|/(ja/)/|/(ko/)' 
        set ambiwidth=double 
    endif 
    if has("win32") 
        source $VIMRUNTIME/delmenu.vim 
        source $VIMRUNTIME/menu.vim 
        language messages zh_CN.utf-8 
    endif 
else 
    echoerr "Sorry, this version of (g)vim was not compiled with +multi_byte" 
endif
```

# 修改编辑配置

```
"--------------------------------------------------------------------------------
" 配置VIM标准显示
"--------------------------------------------------------------------------------
set nu!
colorscheme desert 
syntax enable 
syntax on
"set lines=35 columns=118 "窗口大小
set hlsearch        " 高亮显示搜索结果
set incsearch       " 查询时非常方便，如要查找book单词，当输入到/b时，会自动找到
autocmd GUIEnter * simalt ~x "窗口最大
set cursorcolumn
set cursorline
set nobackup
"--------------------------------------------------------------------------------
" 编程相关的设置
"--------------------------------------------------------------------------------
"set completeopt=longest,menu    " 关掉智能补全时的预览窗口
"filetype pluginindenton       " 加了这句才可以用智能补全
set showmatch       " 设置匹配模式，类似当输入一个左括号时会匹配相应的那个右括号
set smartindent     " 智能对齐方式
set tabstop=4       "tab对应的长度
set softtabstop=4   "回退时退回的缩进长度
set shiftwidth=4    " 换行时行间交错使用4个空格
set expandtab       "缩进用空格表示
set noexpandtab     "缩进用制表符表示
set autoindent      " 自动对齐
set ai!             " 设置自动缩进
"自动补全括号
inoremap ( ()<ESC>i
inoremap [ []<ESC>i
inoremap { {}<ESC>i
```