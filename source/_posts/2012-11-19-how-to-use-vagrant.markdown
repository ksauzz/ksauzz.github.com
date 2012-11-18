---
layout: post
title: "Vagrantメモ"
date: 2012-11-19 01:09
comments: true
categories: vagrant
---

### create vagrant box using veewee.

```
$ vagrant basebox templates
The following templates are available:
vagrant basebox define '<boxname>' 'CentOS-4.8-i386'
vagrant basebox define '<boxname>' 'CentOS-5.5-i386-netboot'
vagrant basebox define '<boxname>' 'CentOS-5.5-x86_64-netboot'
vagrant basebox define '<boxname>' 'CentOS-5.6-i386'
vagrant basebox define '<boxname>' 'CentOS-5.6-i386-netboot'
...
$ vagrant basebox define 'CentOS-6.2-x86_64-minimal'  'CentOS-6.2-x86_64-minimal'

  *customizing definitions*

$ vagrant basebox build CentOS-6.2-x86_64-minimal
$ vagrant basebox export CentOS-6.2-x86_64-minimal
$ vagrant box add CentOS-6.2-x86_64-minimal CentOS-6.2-x86_64-minimal.box
```

### create and connect to VM.

```
$ vagrant init CentOS-6.2-x86_64-minimal
$ vagrant up
$ vagrant ssh
```

### no-provision

```
vagrant up --no-provision
```

### debugging

verbose logging.
```
VAGRANT_LOG=INFO vagrant up
```

check console with gui mode.
```
config.vm.boot_mode = :gui
```

### default account

id/pass

```
vagrant/vagrant
root/vagrant
```
