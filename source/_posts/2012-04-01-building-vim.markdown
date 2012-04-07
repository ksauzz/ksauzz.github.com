---
layout: post
title: "vimのビルド"
date: 2012-04-01 21:47
comments: true
categories: vim
---

ビルドオプションを忘れるのでメモ。mercurialのメモだったりもする。

    hg clone https://vim.googlecode.com/hg/ vim
    cd vim
    hg update v7-3-487
    ./configure --prefix=/usr/local/vim-7.3.487 --with-features=huge --enable-multibyte --enable-pythoninterp --enable-rubyinterp
    make && make install
    cd /usr/local
    ln -s vim-7.3.487 vim
    
### misc

tagの確認
    hg tags
