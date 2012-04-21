---
layout: post
title: "Erlangと周辺技術のメモ"
date: 2012-04-21 20:40
comments: true
categories: erlang
---

gistのメモを移行

## gen\_server

### trace debug

``` erlang 
gen_server:start_link({local, ?SERVER}, ?MODULE, [], [{debug,[trace]}])
```

## rebar

### dependencies in rebar.config


``` erlang rebar.config 
{deps, [
  {webmachine, "1.9.0", {git, "https://github.com/basho/webmachine.git", {tag,"1.9.0"}}}
]}.
```

### generate skelton codes

* ./rebar create-app appid=xxx
* ./rebar create template=simplemod modid=xxx
* ./rebar create template=simplesrv srvid=xxx\_serve

### packaging

``` sh
mkdir rel
cd rel
../rebar create-node nodeid=ememcached
../rebar generate
```

see: [https://github.com/basho/rebar/tree/master/priv/templates](https://github.com/basho/rebar/tree/master/priv/templates)

## dialyzer

Static analysis tool. [http://www.erlang.org/doc/man/dialyzer.html](http://www.erlang.org/doc/man/dialyzer.html)

### build plt file

    dialyzer --build_plt --apps erts kernel stdlib

### extend plt file

    dialyzer --add_to_plt --apps mnesia

### analyse sources

    dialyzer --src src/*.erl

## typer

Type anotator. 

### show type 

    typer -I include src/*.erl

### insert type into the erlang codes.

    typer --annotate -I include src/*.erl
