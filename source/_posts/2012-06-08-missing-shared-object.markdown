---
layout: post
title: "cannot open shared object fileの対処"
date: 2012-06-08 20:10
comments: true
categories: linux
---

Linuxでshared objectが見つからない時の対処方法。いい加減メモっとく。

## 事象

svnを叩いたら libaprutil-1.so が見つからないと起こられた。

    svn: error while loading shared libraries: libaprutil-1.so.0: cannot open shared object file: No such file or directory

ちなみにapacheを/usr/local/apacheへインストールしたのにライブラリの検索パスを通してない事が原因。ということでパスを通しましょう。

## 対応方法

### 1. lddでリンクしているライブラリを調べる

やらなくても良いけど、とりあえず眺めとく。

    $ ldd `which svn`
    
    -- 略 --
    
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found
    libaprutil-1.so.0 => not found
    libapr-1.so.0 => not found

はい、確かに見つかってないです。

### 2. ライブラリの検索パス追加

/etc/ld.so.conf.d/apache.conf を作成して以下を書く。

    /usr/local/apache/lib

もしくは /etc/ld.so.conf へ直接追記してもOK.

### 3. リンクとキャッシュ(/etc/ld.so.cache)の更新

ldconfig実行。

    $ ldconfig

-v オプションで詳細が出力されます。

### 4. 動作確認

パスが通ったか確認。

    $ ldconfig -p | grep libapr
    libaprutil-1.so.0 (libc6,x86-64) => /usr/local/apache/lib/libaprutil-1.so.0
    libaprutil-1.so.0 (libc6) => /usr/lib/libaprutil-1.so.0
    libaprutil-1.so (libc6,x86-64) => /usr/local/apache/lib/libaprutil-1.so
    libapr-1.so.0 (libc6,x86-64) => /usr/local/apache/lib/libapr-1.so.0
    libapr-1.so.0 (libc6) => /usr/lib/libapr-1.so.0
    libapr-1.so (libc6,x86-64) => /usr/local/apache/lib/libapr-1.so

はい、認識されました。

svnを実行してみます。

    $ svn
    使用方法を知りたいときは 'svn help' と打ってください。

無事実行できました。

## 参考
* [ld.so](http://linuxjm.sourceforge.jp/html/LDP_man-pages/man8/ld.so.8.html)
* [ldconfig](http://linuxjm.sourceforge.jp/html/LDP_man-pages/man8/ldconfig.8.html)

