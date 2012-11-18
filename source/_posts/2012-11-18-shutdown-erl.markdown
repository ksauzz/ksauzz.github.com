---
layout: post
title: "erlの停止"
date: 2012-11-18 23:06
comments: true
categories: erlang
---

erlangシェルを停止させるとき、`q()`だと停止に時間が掛かり、`<Crt+G>, q`だと即座に停止する。
この２つの違いを先日追いかけたので記録しておく。

### q() の場合

まずはerlシェル上でq()を呼び出した場合の挙動。

```
Eshell V5.9.2  (abort with ^G)
1> q().
ok
```

マニュアルによるとこれは`shell_default:q/0`を呼び出している。

``` erlang lib/stdlib/src/shell_default.erl
q()_____-> c:q().
```

最終的に`init:stop/0`

```erlang stdlib/src/c.erl
q() ->
    init:stop().
```

`init:stop/0`はコードが負えなかったので、`man init`を読む。すると

{% blockquote %}
All applications are taken down smoothly, all code is unloaded, and all ports are closed before the system terminates.
{% endblockquote %}

となっており、アプリケーションを正式な手順で停止させる雰囲気。

追記: たぶん`erts/preloaded/src/init.erl`から更に追える。

### <Crl+G>, q の場合

次にCrl+G, qの場合。(JCLモードで`q`)

```
Eshell V5.9.2  (abort with ^G)
1>
User switch command
 --> q
```

`User switch command`でgrepすると`user_drv.erl`が使われている様子。

```erlang lib/kernel/src/user_drv.erl
switch_cmd({ok,[{atom,_,q}],_}, Iport, Oport, Gr) ->
    case erlang:system_info(break_ignored) of
    true ->_________________% noop
        io_request({put_chars,unicode,"Unknown command\n"}, Iport, Oport),
        switch_loop(Iport, Oport, Gr);
    false ->
        halt()
    end;
```

`man erlang`より

{% blockquote %}
For  integer  Status the Erlang runtime system closes all ports and allows async threads to finish their operations before exiting.
{% endblockquote %}


### まとめ

* `q()` はapplicationの停止、コードのunloadをした上でruntimeを停止
* `<Crl+G>, q`は単にruntimeを停止




諸々調べた後でマニュアルに停止方法が纏めてあることに気づいたが、
なぜか`q()`が紹介されていなかった。

{% blockquote %}
4.4  How do I quit the Erlang shell?
  To shut a system down cleanly,  use init:stop().
  Some quick ways are evaluating halt(). or Control+\.
  Control+C and Control+G give you access to menus.
{% endblockquote %}

そういえば`erts/preloaded`にはerlang, prim_inet, initモジュール等が入っているので追っかけてみると楽しいかもしれない。
