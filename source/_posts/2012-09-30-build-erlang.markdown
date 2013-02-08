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
    git checkout -b OTP_R15B03 OTP_R15B03
    ./otp_build autoconf
    ./otp_build configure --disable-hipe --enable-smp-support --enable-threads --enable-kernel-poll  --enable-darwin-64bit --prefix=/usr/local/erlang/OTP_R15B03
    make && make install

PREFIXはお好みで。

あとは適当にPATHとか足しとく

#### DTrace support

`--with-dynamic-trace=dtrace`をconfigureのオプションに追加する。

```
./otp_build configure --with-dynamic-trace=dtrace --disable-hipe --enable-smp-support --enable-threads --enable-kernel-poll  --enable-darwin-64bit --prefix=/usr/local/erlang/OTP_R15B03
```
