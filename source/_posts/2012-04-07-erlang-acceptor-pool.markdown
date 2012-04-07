---
layout: post
title: "Erlang/OTPを利用したAcceptor Poolの実装"
date: 2012-04-07 16:19
comments: true
categories: erlang
---

サーバを実装する際にはpreforkモデル云々でacceptor pool(worker pool?)を実装する事がある。
これをErlang/OTPで実装するにあたり、いくつかのOSSコードを参考にさせて頂いた。
これはその参考にさせて貰ったMochiweb, Kaiのacceptor pool実装に関するメモ。

## [Mochiweb](https://github.com/mochi/mochiweb) 
MochiwebはMochi Media, Inc.が公開しているのErlang製の軽量Http Server.というかWeb Framework。

mochiwebではproc\_libを使ってacceptor poolを実装している。
acceptorがクラッシュしてもsupervisorに頼らずに直接再起動をする実装がなされている。 

MochiwebのCall sequence.[拡大図](https://gist.github.com/1981423#file_mochiweb.jpg)
{% img https://raw.github.com/gist/1981423/11f3715f369203dcbc3ecb9d06efb4639d5ceee4/mochiweb.jpg 1134 597 mochiweb call sequence %}

実際にacceptor poolを生成しているのはmochiweb\_socket\_server:new\_acceptor\_pool/2。
ここでmochiweb\_acceptor経由でproc\_lib:spawn\_link/3を連続的に実行することでacceptor poolを生成している。

{% codeblock mochiweb_socket_server:new_acceptor_pool/1 lang:erlang %}
new_acceptor_pool(Listen,
                  State=#mochiweb_socket_server{acceptor_pool=Pool,
                                                acceptor_pool_size=Size,
                                                loop=Loop}) ->
    F = fun (_, S) ->
                Pid = mochiweb_acceptor:start_link(self(), Listen, Loop),
                sets:add_element(Pid, S)
        end,
    Pool1 = lists:foldl(F, Pool, lists:seq(1, Size)),
    State#mochiweb_socket_server{acceptor_pool=Pool1}.
{% endcodeblock %}

acceptorがクラッシュした場合にはmochiweb\_server\_socketでクラッシュのメッセージを受け取り、
ここでacceptorを起動する仕組みになっている。当然、process\_flag(trap\_exit, true)は事前に呼ばれている。

## [Kai](http://sourceforge.net/projects/kai/develop) 
国産のErlangで実装されたKVS. Amazon Dynamoクローン。[slideshare](http://www.slideshare.net/takemaru/kai-an-open-source-implementation-of-amazons-dynamo-in-japanese)

Kaiはsupervisorを利用してacceptor poolを実装している。supervisor:start\_linkにacceptorの定義をリストにして渡すことで、
supervisorのworkerとしてacceptorを多数起動し、これによりpoolを実現している。当然、acceptorの再起動もSupervisor任せである。

acceptor定義のListを生成しているのは以下のkai\_tcp\_sup:acceptor\_spec/6である。
{% codeblock kai_tcp_server_sup:acceptor_specs/6 lang:erlang %}
acceptor_specs(
  ListenSocket, State, {Dest, Name}, MonitorBaseName, Mod, Option
) ->
    #tcp_server_option{
        max_processes = MaxProcesses,
        shutdown      = Shutdown
    } = Option,
    MonitorName = case Dest of
      local   -> MonitorBaseName;
      _Global -> {Dest, MonitorBaseName}
    end,
    lists:map(
        fun (N) ->
            AcceptorName = build_acceptor_name(Name, N),
            {
                AcceptorName,
                {
                    kai_tcp_server_acceptor,
                    start_link,
                    [
                        {Dest, AcceptorName},
                        ListenSocket,
                        State,
                        MonitorName,
                        Mod,
                        Option
                    ]
                },
                permanent,
                Shutdown,
                worker,
                []
            }
        end,
        lists:seq(1, MaxProcesses)
    ).
{% endcodeblock %}

## まとめ

### Acceptorの生成

- Mochiweb: proc\_libで個別に起動
- kai: supervisor任せ

### Acceptorの再起動
- Mochiweb: 自前実装
- kai: supervisor任せ


なんか他のコードも読んだ気がしたけど、すっかり忘れてしまったので以上２つだけのまとめ。

ちなみに自分が実装する際にはKaiの方式を利用させて貰った。Supervisor便利である。
