---
layout: post
title: "デフォルトディレクトリ以外へのHomebrewインストール"
date: 2012-06-27 03:15
comments: true
categories: mac
---

Homebrewはデフォルトで /usr/local へインストールされるが、
これを /usr/local/homebrew へインストールしたときの手順

## インストール

    cd /usr/local
    sudo git clone git://github.com/mxcl/homebrew.git homebrew
    sudo chgrp -R staff homebrew bin include lib
    sudo chmod -R 775 homebrew bin include lib
    ln -s /usr/local/homebrew/bin/brew /usr/local/bin/brew

MANPATHを通しておく(.zshenv)
    export MANPATH=$MANPATH:/usr/local/homebrew/share/man

Homebrewはドキュメントにもあるように /usr/local にシンボリックリンクを作らなければならないので、
/usr/local/homebrew/bin へPATHを通してはいけない。

> Everything will install into ~/Developer, but your brew command is still in the path.
> NOTE that Homebrew will still need to create symlinks into /usr/local or nothing will work!
> But the actual files are installed to ~/Developer/Cellar.

## デフォルトインストール時(/usr/local)のアンインストール手順

アンインストールの手段が提供されていないので、個別にインストールファイルを削除する。

gistにアンインストール用のスクリプトが公開されているので、これを使うと便利。

[https://gist.github.com/1173223](https://gist.github.com/1173223)
