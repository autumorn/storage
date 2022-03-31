---
title: vim configuration
description: vim的最常用配置
date: 2020-09-09
categories:
    - 配置
tags:
    - 工具
---

### 基本配置
vim在不安装组件的情况下拥有很多基本的功能，首先我们需要对常用的基本配置进行设置，基本配置已经足够日常使用，基本配置及其相关注释如下：

```
" Basic Settings
set nocompatible
" detect file type
filetype on
" load plugin for detected file type
filetype plugin on
" load indent file for detected file type
filetype indent on
" syntax highlight
syntax on
set encoding=UTF-8
" Add numbers to each line on the left-hand side.
set number
" highlight current line
set cursorline
" Set shift width to 4 spaces.
set shiftwidth=2
" Set tab width to 4 columns.
set tabstop=4
" Use space characters instead of tabs.
set expandtab
" Set indent style
set cindent
set smartindent
set autoindent
" Do not save backup files.
set nobackup
" Do not let cursor scroll below or above N number of lines when scrolling.
set scrolloff=10

" set font
set guifont=DroidSansMono_Nerd_Font:h11

" Use highlighting when doing a search.
set hlsearch
" While searching though a file incrementally highlight matching characters as you type.
set incsearch
" Ignore capital letters during search.
set ignorecase
" Override the ignorecase option if searching for capital letters.
" This will allow you to search specifically for capital letters.
set smartcase

" Show partial command you type in the last line of the screen.
set showcmd
" Show the mode you are on the last line.
set showmode
" Show matching words during a search.
set showmatch

" Set the commands to save in history default number is 20.
set history=1000
" Enable auto completion menu after pressing TAB.
set wildmenu
" Make wildmenu behave like similar to Bash completion.
set wildmode=list:longest
" There are certain files that we would never want to edit with Vim.
" Wildmenu will ignore files with these extensions.
set wildignore=*.docx,*.jpg,*.png,*.gif,*.pdf,*.pyc,*.exe,*.flv,*.img,*.xlsx
```

### 插件安装
使用[vim_plug](https://github.com/junegunn/vim-plug)插件管理器，并在vimrc配置文件中添加如下代码：

```
" -------------------------------- Plugin -------------------------------------------
" Specify a directory for plugins
" - For Neovim: stdpath('data') . '/plugged'
" - Avoid using standard Vim directory names like 'plugin'
call plug#begin('~/.vim/plugged')



" Initialize plugin system
call plug#end()
```

#### 常用插件及管理
将插件的名称与github上的仓库加入call plugin begin 与 end之间即可， 然后打开vim使用PlugInstall进行安装，插件管理命令如下所示：

|  Command |  Description |
|---|---|
|  PlugInstall [name ...] [#threads] |   Install plugins |
|  PlugUpdate [name ...] [#threads] |   Install or update plugins |
|   PlugClean[!] |   Remove unlisted plugins (bang version will clean without prompt)|
| PlugUpgrade	|Upgrade vim-plug itself|
|PlugStatus|Check the status of plugins|
|PlugDiff	|Examine changes from the previous update and the pending changes|

##### NERDTree
主要安装NERDTree以及git组件，可以在目录中显示git状态，加入如下两行代码进行安装：

```
Plug 'scrooloose/nerdtree', { 'on':  'NERDTreeToggle' }
Plug 'Xuyuanp/nerdtree-git-plugin'
```
插件相关配置如下：

```
" set NerdTree plugin
nnoremap <leader>n :NERDTreeFocus<CR>
nnoremap <C-n> :NERDTree<CR>
nnoremap <C-t> :NERDTreeToggle<CR>
" nnoremap <C-f> :NERDTreeFind<CR>

" Start NERDTree when Vim starts with a directory argument.
autocmd StdinReadPre * let s:std_in=1
autocmd VimEnter * if argc() == 1 && isdirectory(argv()[0]) && !exists('s:std_in') |
    \ execute 'NERDTree' argv()[0] | wincmd p | enew | execute 'cd '.argv()[0] | endif
" Exit Vim if NERDTree is the only window remaining in the only tab.
autocmd BufEnter * if tabpagenr('$') == 1 && winnr('$') == 1 && exists('b:NERDTree') && b:NERDTree.isTabTree() | quit | endif
" Close the tab if NERDTree is the only window remaining in it.
autocmd BufEnter * if winnr('$') == 1 && exists('b:NERDTree') && b:NERDTree.isTabTree() | quit | endif
" Start NERDTree. If a file is specified, move the cursor to its window.
autocmd StdinReadPre * let s:std_in=1
autocmd VimEnter * NERDTree | if argc() > 0 || exists("s:std_in") | wincmd p | endif
```

#### Taglist
功能：利用ctags生成当前文件所有的类以及类型符号
使用taglist需要满足的条件有：

* 打开文件的自己检测功能：filetype on
* 系统中安装exuberant ctags工具 
* 并且vim支持system()调用

然后在vimrc中添加安装taglist的地址：

```
Plug 'vim-scripts/taglist.vim'
```

安装之后进行相关的配置：

```
" set TagList plugin
let Tlist_Ctags_Cmd="/usr/local/bin/ctags"
let Tlist_Use_Right_Window = 1
let Tlist_Exit_OnlyWindow = 1
let Tlist_Show_One_File = 1
" let Tlist_Sort_Type = "name"
let Tlist_Auto_Open = 1
```

#### DelimitMate
功能：自动匹配括号与引号
安装地址及相关配置如下：

```
Plug 'Raimondi/delimitMate'

" set DelimitMate plugin
" close auto close function
" let b:delimitMate_autoclose = 0
" close auto close function for python files
" au FileType python let b:delimitMate_autoclose = 0
let delimitMate_expand_cr = 1
```

#### ctags & cscope & srcexpl
功能：跳转功能的实现，方便在编写代码的时候查看类或者其它结构的实现
安装srcexpl插件：

```
Plug 'wenlongche/SrcExpl'
```

安装后的配置如下:

```
" set ctags generation and path
function Generate_cstag()
  if(executable('cscope') && has("cscope"))
    silent! execute "!find -L `pwd` -name '*.h' -o -name '*.c' -o -name '*.cc' -o -name '*.cpp' -o -name '*.java' -o -name '*.cxx' -o -name '*.hxx' -o -name '*.hpp' -o -name '*.py' > cscope.files"
  endif
  silent! execute "!ctags -L cscope.files"
  silent! execute "!cscope -bkq -i cscope.files"
  silent! execute "call Add_cstag()"
endf

function Add_cstag()
  if filereadable("cscope.out")
    execute "cs add cscope.out"
    execute "set tags+=./tags"
  endif
endf

silent! execute "call Add_cstag()"

map <F10> :call Generate_cstag()<CR>
map <F10><F10> :call Add_cstag()<CR>
nmap <C-\>s :cs find s <C-R>=expand("<cword>")<CR><CR>
nmap <C-\>g :cs find g <C-R>=expand("<cword>")<CR><CR>
nmap <C-\>c :cs find c <C-R>=expand("<cword>")<CR><CR>
nmap <C-\>t :cs find t <C-R>=expand("<cword>")<CR><CR>
nmap <C-\>e :cs find e <C-R>=expand("<cword>")<CR><CR>
nmap <C-\>f :cs find f <C-R>=expand("<cword>")<CR><CR>
nmap <C-\>i :cs find i <C-R>=expand("<cword>")<CR><CR>
nmap <C-\>d :cs find d <C-R>=expand("<cword>")<CR><CR>

" set srcexpl plugin
nmap <F8> :SrcExplToggle<CR>
let g:SrcExpl_winHeight = 16
let g:SrcExpl_refreshTime = 100
let g:SrcExpl_jumpKey = "<ENTER>"
let g:SrcExpl_gobackKey = "<SPACE>"
let g:SrcExpl_searchLocalDef = 1
let g:SrcExpl_isUpdateTags = 0
let g:SrcExpl_updateTagsCmd = "ctags --sort=foldcase -R ."
let g:SrcExpl_updateTagsKey = "<F12>"
let g:SrcExpl_prevDefKey = "<F3>"
let g:SrcExpl_nextDefKey = "<F4>"
```
