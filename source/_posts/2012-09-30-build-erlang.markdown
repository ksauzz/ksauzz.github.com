---
layout: post
title: "Erlangのビルド"
date: 2012-09-30 22:56
comments: true
categories: erlang
---

OSXへErlangをソースからいれた時のメモ。
--enable-darwin-64bitつけないと後でトラブるよ。という事でメモ。

    git clone git://github.com/erlang/otp.git
    cd otp
    git checkout -b OTP_R15B02 OTP_R15B02
    ./otp_build autoconf
    ./otp_build configure --enable-hipe --enable-smp-support --enable-threads --enable-kernel-poll --enable-darwin-64bit --prefix=/usr/local/erlang/OTP_R15B02
    make && make install

PREFIXはお好みで。

あとは適当にPATHとか足しとく
