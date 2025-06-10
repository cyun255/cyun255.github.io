---
title: Neovim的IDE之路 | 简单配置
date: 2023-04-21
comments: false
categories: [Linux, Neovim]
tags:
  - Vim
  - Linux
  - Neovim
description: |
---

## 前言

前面已经了解了最最基本的Vim操作方式，简单的使用Vim已经没问题了，但是初始的Vim过于简陋，需要简单的配置一下

打开Vim时会自动加载配置文件，配置文件路径是固定的：
- Vim的配置文件路径：`~/.config/vim/vimrc`
- Neovim的配置文件路径：`~/.config/nvim/init.vim`

## 设置选项

### 行号设置

首先，作为一个编辑器至少需要显示行号。除了绝对行号之外，(Neo)Vim还支持显示相对行号，两种方式同时启动会开启混合模式。

混合模式下当前光标所在行显示绝对行号，其他行显示相对行号，相对行号非常契合Vim的操作方式。
比如，要删除一段文本通过相对行号能够直接看出要向下删除 `n` 行，然后直接 `dnj` 就能完成删除操作

```vim
" 显示行号
set number

" 显示相对行号
set relativenumber

" 设置行号显示的宽度
set numberwidth=4
```

### 文本缩进

(Neo)Vim默认一个 `<tab>` 相当于8个空格，通常一个 `<tab>` 对应两个或四个空格比较合适

```vim
" 设置一个 `<tab>` 宽度为4个空格
set tabstop=4
set softtabstop=4

" 设置手动使用 `>>` 或 `<<` 时缩进4个空格
set shiftwidth=4

" 设置文本缩进长度为 shiftwidth 的倍数
set shiftround

" 设置自动缩进
" (Neo)Vim 默认有三种自动缩进模式 cindent, autoindent, smartindent
set smartindent
```

### 文本高亮

代码高亮是非常重要的，同时光标的位置也应该能够直观的凸显出来

```vim
" 设置开启代码高亮，Neovim默认开启
set syntax

" 高亮光标所在行
set cursorline

" 高亮光标所在列
set cursorcolumn
```

使用 `/` 或 `?` 查找单词时，通常也会希望将匹配到的结果高亮显示

```vim
" 高亮匹配的文本
set hlsearch

" 搜索时忽略大小写
set ignorecase

" 搜索包含大写字母时，严格匹配
set smartcase
```

### 其他设置

还有一些不知道要怎么分类，但是个人感觉很有用的功能

```vim
" 文本超出屏幕，不换行显示
set nowrap

" 屏幕上下保留行，让眼睛能够看着屏幕中间
set scrolloff=10

" 命令模式使用 `<tab>` 补全时，显示选项卡
set showcmd

" 右下角显示搜索索引
set shortmess-=S

" 允许光标自动换行
set whichwrap+=<,>,h,l

" 使用系统剪辑版
set clipboard+=unnamedplus

" 允许光标移动到行尾
set virtualedit=block,onemore
```

## 键盘映射

(Neo)Vim 通过使用 `nmap`、`imap` 以及 `vmap` 命令分别在 Normal、Insert 和 Visual 模式生效。

`*map` 命令虽然方便，但是会导致递归操作，使用 `*noremap` 可以避免映射内容递归运行

### Leader

(Neo)Vim 可以通过配置 `mapleader` 变量来配置前缀键，使用 `<leader>` 引用，使用前缀键能够避免快捷键与 Normal 模式下的操作方式冲突

```vim
let mapleader = ","
```

### Esc

使用 (Neo)Vim 编辑文件时存在一个问题，每一次切换模式都需要左手离开现在的位置去按 `<esc>`。
比较推荐使用两个不容易同时出现的字母代替 `<esc>` 操作，例如：`jj`, `qq`等等

```vim
inoremap qq <esc>
```

### 分屏

(Neo)Vim 可以在 Command 模式下使用 `:split` 和 `:vsplit` 命令水平或垂直分屏，配合 `:e filepath` 打开其他文件

可以在 Normal 模式下建立一个映射，快速的完成分屏操作

```vim
nnoremap <leader>si :split<cr>
nnoremap <leader>su :vsplit<cr> 
```

完成分屏之后，还需要让光标能够在多个文件之间进行切换

(Neo)Vim 自带的命令是使用 `<c-w>` + `h`, `j`, `k`, `l` 进行切换，每次都需要多按一次 `<c-w>`

可以设置映射使用 `<c-h>` 代替 `<c-w>h`，操作方便的同时也契合 `h` 光标向左移动的特性

```vim
nnoremap <c-h> <c-w>h
nnoremap <c-j> <c-w>j
nnoremap <c-k> <c-w>k
nnoremap <c-l> <c-w>l
```

### 大小

(Neo)Vim 分屏一般会平分屏幕的高度或高度，可以使用命令手动的修改屏幕大小

既然 `hjkl` 可以移动光标了，那么小键盘上下左右键失去了原先的意义，就很适合用来修改分屏的大小

```vim
nnoremap <up> :res +5<cr>
nnoremap <down> :res -5<cr>
nnoremap <left> :vertical resize-5<cr>
nnoremap <right> :vertical resize+5<cr>
```

### 标签

(Neo)Vim 还支持打开多个标签，通过键盘映射快速的插件、切换以及关闭标签

```vim
nnoremap <leader>tt :tabnew<cr>
nnoremap <leader>tn :tabNext<cr>
nnoremap <leader>tp :tabprevious<cr>
nnoremap <leader>tc :tabclose<cr>
```

## 杂项

```vim
augroup init
	autocmd!

	" 代码折叠
	autocmd FileType vim setlocal foldmethod=marker

	" 打开文件时，恢复上一次关闭文件时光标所在行
	autocmd BufReadPost * if line("'\"") > 1 && line("'\"") <= line("$") | execute "normal! g'\"" | endif

augroup END
```
