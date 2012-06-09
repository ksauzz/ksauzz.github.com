---
layout: post
title: "rbenv ruby-build rbenv-gemsetのセットアップ"
date: 2012-03-23 21:53
comments: true
categories: ruby
---

近頃、RVMはオワコン呼ばわりされているので、rbenvを使ってます。
Linuxへのインストール手順のまとめが見当たらなかったのでまとめておきます。

# MacOS

    sudo brew install rbenv ruby-build rbenv-gemset

## rbenv with homebrew's readline

参考： [http://blog.twiwt.org/e/66a1d0](http://blog.twiwt.org/e/66a1d0)

    brew install readline
    brew link readline
    CONFIGURE_OPTS="--with-readline-dir=/usr/local" rbenv install 1.9.2-p290

# Linux

## install rbenv

ダウンロード

    cd
    git clone git://github.com/sstephenson/rbenv.git .rbenv

シェルの設定(zsh)

    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
    echo 'eval "$(rbenv init -)"' >> ~/.zshrc
    source ~/.zshrc

*bashの場合は ~/.bash_profileへ設定する.*

### system wide install

System wideでインストールする場合には /usr/local/rbenv へインストールします。

    cd /usr/local
    git clone git://github.com/sstephenson/rbenv.git

初期設定は /etc/profile.d/rbenv.sh へいれます。

    echo 'export PATH="/usr/local/rbenv/bin:$PATH"' >> /etc/profile.d/rbenv.sh
    echo 'eval "$(rbenv init -)"' >> /etc/profile.d/rbenv.sh

参考：[shared-install-of-rbenv](https://github.com/sstephenson/rbenv/wiki/shared-install-of-rbenv)

### trouble shoot

シェル初期化時に以下のエラーが発生することがある。

    $HOME/.rbenv/libexec/../completions/rbenv.bash:14: command not found: complete

この場合、.zshrcの設定を以下に変更

    eval "$(rbenv init - zsh)"

## install ruby-build

    mkdir -p ~/.rbenv/plugins
    cd ~/.rbenv/plugins
    git clone git://github.com/sstephenson/ruby-build.git

## install rbenv-gemset

    mkdir -p ~/.rbenv/plugins
    cd ~/.rbenv/plugins
    git clone git://github.com/jamis/rbenv-gemset.git

# Usage

## install ruby

    rbenv install 1.9.2-p290
    rbenv global 1.9.2-p290
    rbenv rehash

## freeze ruby version

    cd your-project-dir
    rbenv local 1.9.2-p290

## make my gemset

    cd your-project-dir
    rbenv gemset create 1.9.2-p290 my-gemset 
    echo my-gemset > .rbenv-gemsets
