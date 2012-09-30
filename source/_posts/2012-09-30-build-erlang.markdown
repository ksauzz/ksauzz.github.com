---
layout: post
title: "Erlangのビルド"
date: 2012-09-30 22:56
comments: true
categories: erlang
---

Erlangをソースからいれた時のメモ

    git clone git://github.com/erlang/otp.git
    cd otp
    ./otp_build autoconf
    ./otp_build configure --enable-hipe --enable-smp-support --enable-threads --enable-kernel-poll --enable-darwin-64bit --prefix=/usr/local/erlang/OTP_R15B02

あとは適当にPATHとか足しとく
