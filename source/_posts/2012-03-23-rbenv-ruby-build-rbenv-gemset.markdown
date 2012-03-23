---
layout: post
title: "rbenv ruby-build rbenv-gemsetのセットアップ"
date: 2012-03-23 21:53
comments: true
categories: ruby
---

近頃、RVMは散々オワコン呼ばわりされているので、とりあえずrbenvを使ってます。
Linuxへのインストール手順のまとめが見当たらなかったので自分でまとめておきます。

# MacOS

    sudo brew install rbenv ruby-build rbenv-gemset

# Linux

Homebrewを使わないセットアップ方法のまとめ。

## install rbenv

ダウンロード

    cd 
    git clone git://github.com/sstephenson/rbenv.git .rbenv

シェルの設定(zsh)
    echo 'export PATH="$HOME/.rbenv/bin:$PATH"' >> ~/.zshrc
    echo 'eval "$(rbenv init -)"' >> ~/.zshrc
    source ~/.zshrc

*bashの場合は ~/.bash_profileへ設定する.*

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
