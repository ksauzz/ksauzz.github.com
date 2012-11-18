---
layout: post
title: "Installing http\_trace on CentOS"
date: 2012-11-15 11:56
comments: true
categories: http, tool
---

コピペ用

```
wget http://nodejs.tchol.org/repocfg/el/nodejs-stable-release.noarch.rpm
yum localinstall --nogpgcheck -y nodejs-stable-release.noarch.rpm
yum install -y nodejs-compat-symlinks npm libpcap-devel
npm -g install http_trace
```
