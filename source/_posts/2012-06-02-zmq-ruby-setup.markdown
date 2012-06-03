---
layout: post
title: "ZeroMQ Rubyバインディングのインストール"
date: 2012-06-02 18:34
comments: true
categories: ruby zeromq
---

そろそろ[ZeroMQ](http://www.zeromq.org/)を触ろうか、ということでRubyバインディングをとりあえずインストール。

ZeroMQのRubyバインディングは[rbzmq](https://github.com/zeromq/rbzmq)と[ffi-rzmq](https://github.com/chuckremes/ffi-rzmq)があります。
rbzmqは公式のRubyバインディングでffi-rzmqは[ffi](http://www.slideshare.net/copiousfreetime/ffi-building-cross-engine-ruby-extensions)版です。
ffiに関しては、さっぱり詳しくないのですが、
MatzRuby使ってる分には基本rbzmqの方で良いのかなと思ってます。
ffi-rzmqをインストールしたのは[learn-ruby-zeromq](https://github.com/andrewvc/learn-ruby-zeromq)を動かしたかった、というだけの理由です。

以下、MacOSでのお話…。

## ZeroMQのインストール

とりあえずZeroMQをインストールします。
特になにも考えず現在のStable版である2.2.0を入れました。
今回はソースビルドしていて、インストール先は/usr/local/zmqです。

    git clone https://github.com/zeromq/zeromq2-x.git
    cd zeromq2-x
    git checkout v2.2.0
    ./autogen.sh
    ./configure --prefix=/usr/local/zmq
    make
    sudo make install

## rbzmqのインストール

--with-zmq-dirでインストールパスを指定します。

    gem install zmq -- --with-zmq-dir=/usr/local/zmq

## ffi-rzmqのインストール

    gem install ffi-rzmq

    echo "export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/zmq/lib" >> ~/.zshrc

/usr/local/zmqへインストールしたので、LD\_LIBRARY\_PATHの指定をしておかないと実行時にコケます。


インストール作業は以上。
