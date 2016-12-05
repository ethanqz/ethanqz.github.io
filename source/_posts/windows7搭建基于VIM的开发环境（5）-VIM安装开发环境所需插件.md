---
title: windows7搭建基于VIM的开发环境（5）- VIM安装开发环境所需插件
categories: 开发环境搭建
tags: [vim,windows,x86_64]

---

> 本系列指导 主要是从无到有完整搭建windows下基于VIM的开发环境，主要分为以下几部分：

- **搭建msys2下64位编译环境**
- **编译64位且支持python的VIM版本并安装**
- **VIM基本配置**
- **VIM安装vundle**
- **VIM安装开发环境所需插件**<<font color=red>本篇</font>>

>本篇我们主要讲解vim安装开发环境所需插件。其中jedi-vim和syntastic需要vim添加python支持，neocomplete需要vim添加lua支持，在[第二篇](http://qz757.github.io/2016/12/04/windows7%E6%90%AD%E5%BB%BA%E5%9F%BA%E4%BA%8EVIM%E7%9A%84%E5%BC%80%E5%8F%91%E7%8E%AF%E5%A2%83%EF%BC%882%EF%BC%89-%E7%BC%96%E8%AF%9164%E4%BD%8D%E4%B8%94%E6%94%AF%E6%8C%81python%E7%9A%84VIM%E7%89%88%E6%9C%AC%E5%B9%B6%E5%AE%89%E8%A3%85/)我们已经完成了vim编译的python和lua的支持。

#安装依赖
1. 安装pip，[pip官方地址](https://pypi.python.org/pypi/pip)下载最新版pip-9.0.1.tar.gz，下载后解压并运行`python setup.py install`。
2. 配置pip，在HOME目录下新建pip文件夹，并创建pip.ini，增加软件源配置：
```
[global]
index-url = http://pypi.douban.com/simple
[install]
trusted-host = pypi.douban.com
```
3. 安装pyflakes，`pip install pyflakes`,syntastic代码检查依赖。
4. 安装jedi，`pip install jedi`，jedi-vim代码不全依赖。

# 所有插件列表
```
"--------------------------------------------------------------------------------
" 插件安装列表
"--------------------------------------------------------------------------------
Bundle 'scrooloose/nerdtree'
Bundle 'majutsushi/tagbar'
Bundle 'fholgado/minibufexpl.vim'
Bundle 'scrooloose/syntastic'
Bundle 'a.vim'
Bundle 'c.vim'
Bundle 'grep.vim'
Bundle 'shougo/neocomplete'
Bundle 'kien/ctrlp.vim'
Bundle 'SuperTab'
Bundle 'davidhalter/jedi-vim'
Bundle 'chazy/cscope_maps'
```
在_vimrc增加插件列表配置后，重启vim，运行:PluginInstall，待安装完成后，按照以下步骤增加各插件的配置。
# ctags
```
"--------------------------------------------------------------------------------
" ctags
"--------------------------------------------------------------------------------
set tags=tags; 
set autochdir
```
# NERDTree
```
"--------------------------------------------------------------------------------
" NERDTree
"--------------------------------------------------------------------------------
"NERDTree快捷键
"nmap <F2> :NERDTreeToggle  <CR>
autocmd bufenter * if (winnr("$") == 1 && exists("b:NERDTreeType") &&b:NERDTreeType == "primary") | q | endif
autocmd vimenter * NERDTree
" NERDTree.vim
let g:NERDTreeWinPos="right"
let g:NERDTreeWinSize=30
let g:NERDTreeShowLineNumbers=1
let g:neocomplcache_enable_at_startup = 1
```
# minibuf
```
"--------------------------------------------------------------------------------
" minibuf
"--------------------------------------------------------------------------------
let g:miniBufExplMapWindowNavVim = 1   
let g:miniBufExplMapWindowNavArrows = 1   
let g:miniBufExplMapCTabSwitchBufs = 1   
let g:miniBufExplModSelTarget = 1  
let g:miniBufExplMoreThanOne=0

nnoremap <C-N> :bn<CR>
nnoremap <C-P> :bp<CR>
nnoremap <C-D> :MBEbd<CR>
```
# tagbar
```
"--------------------------------------------------------------------------------
" tagbar
"--------------------------------------------------------------------------------
"nmap <Leader>tb :TagbarToggle<CR>        "快捷键设置
let g:tagbar_left = 1
let g:tagbar_ctags_bin='ctags.exe'            "ctags程序的路径
let g:tagbar_width=30                    "窗口宽度的设置
map <F3> :Tagbar<CR>
autocmd BufReadPost *.cpp,*.c,*.h,*.hpp,*.cc,*.cxx,*.py call tagbar#autoopen()     "如果是c语言的程序的话，tagbar自动开启
```
# ctags
```
"--------------------------------------------------------------------------------
" ctags
"--------------------------------------------------------------------------------
set tags=tags; 
set autochdir
```
# grep
```
"--------------------------------------------------------------------------------
" grep
"--------------------------------------------------------------------------------
"grep
nnoremap <silent> <F4> :Grep<CR>
```
# a.vim
```
"--------------------------------------------------------------------------------
" a.vim
"--------------------------------------------------------------------------------
"a.vim H/C文件切换
nnoremap <silent> <F12> :A<CR> 
```
# syntastic
```
"--------------------------------------------------------------------------------
" syntastic
"--------------------------------------------------------------------------------
let g:syntastic_error_symbol='>>'
let g:syntastic_warning_symbol='>'
let g:syntastic_check_on_open=1
let g:syntastic_check_on_wq=1
let g:syntastic_enable_highlighting=1
let g:syntastic_python_checkers=['pyflakes'] " 使用pyflakes,速度比pylint快
" 修改高亮的背景色, 适应主题
highlight SyntasticErrorSign guifg=white guibg=red

" to see error location list
let g:syntastic_always_populate_loc_list = 0
let g:syntastic_auto_loc_list = 0
let g:syntastic_loc_list_height = 5
function! ToggleErrors()
    let old_last_winnr = winnr('$')
    lclose
    if old_last_winnr == winnr('$')
        " Nothing was closed, open syntastic error location panel
        Errors
    endif
endfunction
nnoremap <Leader>s :call ToggleErrors()<cr>
" nnoremap <Leader>sn :lnext<cr>
" nnoremap <Leader>sp :lprevious<cr>
```
# jedi-vim
```
"--------------------------------------------------------------------------------
" jedi-vim
"--------------------------------------------------------------------------------
let g:jedi#use_tabs_not_buffers = 1
let g:jedi#use_splits_not_buffers = "bottom"
let g:jedi#show_call_signatures = "1"
```
# neocomplete
```
"--------------------------------------------------------------------------------
" neocomplete
"--------------------------------------------------------------------------------
"Note: This option must set it in .vimrc(_vimrc).  NOT IN .gvimrc(_gvimrc)!
" Disable AutoComplPop.
let g:acp_enableAtStartup = 0
" Use neocomplete.
let g:neocomplete#enable_at_startup = 1
" Use smartcase.
let g:neocomplete#enable_smart_case = 1
" Set minimum syntax keyword length.
let g:neocomplete#sources#syntax#min_keyword_length = 3
let g:neocomplete#lock_buffer_name_pattern = '\*ku\*'

" Define dictionary.
let g:neocomplete#sources#dictionary#dictionaries = {
    \ 'default' : '',
    \ 'vimshell' : $HOME.'/.vimshell_hist',
    \ 'scheme' : $HOME.'/.gosh_completions'
        \ }

" Define keyword.
if !exists('g:neocomplete#keyword_patterns')
    let g:neocomplete#keyword_patterns = {}
endif
let g:neocomplete#keyword_patterns['default'] = '\h\w*'

" Plugin key-mappings.
inoremap <expr><C-g>     neocomplete#undo_completion()
inoremap <expr><C-l>     neocomplete#complete_common_string()

" Recommended key-mappings.
" <CR>: close popup and save indent.
inoremap <silent> <CR> <C-r>=<SID>my_cr_function()<CR>
function! s:my_cr_function()
  return neocomplete#close_popup() . "\<CR>"
  " For no inserting <CR> key.
  "return pumvisible() ? neocomplete#close_popup() : "\<CR>"
endfunction
" <TAB>: completion.
inoremap <expr><TAB>  pumvisible() ? "\<C-n>" : "\<TAB>"
" <C-h>, <BS>: close popup and delete backword char.
inoremap <expr><C-h> neocomplete#smart_close_popup()."\<C-h>"
inoremap <expr><BS> neocomplete#smart_close_popup()."\<C-h>"
inoremap <expr><C-y>  neocomplete#close_popup()
inoremap <expr><C-e>  neocomplete#cancel_popup()
" Close popup by <Space>.
"inoremap <expr><Space> pumvisible() ? neocomplete#close_popup() : "\<Space>"

" For cursor moving in insert mode(Not recommended)
"inoremap <expr><Left>  neocomplete#close_popup() . "\<Left>"
"inoremap <expr><Right> neocomplete#close_popup() . "\<Right>"
"inoremap <expr><Up>    neocomplete#close_popup() . "\<Up>"
"inoremap <expr><Down>  neocomplete#close_popup() . "\<Down>"
" Or set this.
"let g:neocomplete#enable_cursor_hold_i = 1
" Or set this.
"let g:neocomplete#enable_insert_char_pre = 1

" AutoComplPop like behavior.
"let g:neocomplete#enable_auto_select = 1

" Shell like behavior(not recommended).
"set completeopt+=longest
"let g:neocomplete#enable_auto_select = 1
"let g:neocomplete#disable_auto_complete = 1
"inoremap <expr><TAB>  pumvisible() ? "\<Down>" : "\<C-x>\<C-u>"

" Enable omni completion.
autocmd FileType css setlocal omnifunc=csscomplete#CompleteCSS
autocmd FileType html,markdown setlocal omnifunc=htmlcomplete#CompleteTags
autocmd FileType javascript setlocal omnifunc=javascriptcomplete#CompleteJS
autocmd FileType python setlocal omnifunc=pythoncomplete#Complete
autocmd FileType xml setlocal omnifunc=xmlcomplete#CompleteTags

" Enable heavy omni completion.
if !exists('g:neocomplete#sources#omni#input_patterns')
  let g:neocomplete#sources#omni#input_patterns = {}
endif
"let g:neocomplete#sources#omni#input_patterns.php = '[^. \t]->\h\w*\|\h\w*::'
"let g:neocomplete#sources#omni#input_patterns.c = '[^.[:digit:] *\t]\%(\.\|->\)'
"let g:neocomplete#sources#omni#input_patterns.cpp = '[^.[:digit:] *\t]\%(\.\|->\)\|\h\w*::'

" For perlomni.vim setting.
" https://github.com/c9s/perlomni.vim
let g:neocomplete#sources#omni#input_patterns.perl = '\h\w*->\h\w*\|\h\w*::'
```
# 结束语
至此，基于vim的windows下的python开发环境就搭建完成了，其中所有依赖的安装包以及编译好的VIM版本都可以从我的vim仓库：[https://github.com/qz757/vim](https://github.com/qz757/vim)下载。