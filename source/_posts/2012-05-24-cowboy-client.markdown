---
layout: post
title: "cowboy\_clientを使う"
date: 2012-05-24 20:43
comments: true
categories: erlang cowboy
---

ErlangのWebフレームワークCowboyにはcowboy\_clientというクライアントモジュールが含まれています。
今回はこのcowboy\_clientの基本的な使い方を紹介します。

## 利用方法

cowboy\_clientは以下の流れで利用します。

1. clientレコードの生成 (init/1)
2. 接続の確立 (connect/4)
3. リクエスト送信 (request/3, request/4, request/5)
4. レスポンスヘッダーの取得 (response/1)
5. レスポンスの取得 (response\_body/1)

これを実際のコードで表現すると、こんな感じです。

```erlang
{ok, Client1} = cowboy_client:init([]),
{ok, Client2} = cowboy_client:connect(cowboy_tcp_transport, "localhost", 8080, Client1),
{ok, Client3} = cowboy_client:request(<<"GET">>, <<"/contents.html">>, [], Client2),
{ok, Status, Headers, Client4} = cowboy_client:response(Client3),
{ok, Body, Client5} = cowboy_client:response_body(Client4),
```


以降でinit/1以外の各行を個別に確認してゆきます。

### 接続の確立 (connect/4)

Transport, Host, Portを指定してコネクションを生成します。
恐らくSSL通信の場合はcowboy_ssl_transportを利用すると思われます。
```erlang
{ok, Client2} = cowboy_client:connect(cowboy_tcp_transport, "localhost", 8080, Client1),
```

### HTTPリクエスト (request/4)

request関数は３種類用意されています。
リクエストヘッダー、リクエストボディ設定の有無でこれらを使い分けます。

 - request(Method, Url, Client)
 - request(Method, Url, Headers, Client)
 - request(Method, Url, Headers, Data, Client)

```erlang
{ok, Client3} = cowboy_client:request(<<"GET">>, <<"/contents.html">>, [], Client2),
```

なお、Headerは以下のようなリストで指定します。

```erlang
[{<<"connection">>, <<"close">>}]
```

### HTTPレスポンス (response/1, response\_body/1)

ヘッダー、ステータスコードの取得(response/1)とボディの取得(response\_body/1)は、個別に行う必要があります。
必要に応じてコールします。

```erlang
{ok, Status, Headers, Client4} = cowboy_client:response(Client3),
{ok, Body, _Client5} = cowboy_client:response_body(Client4),
```

以上がリクエスト送信からレスポンス受信までの流れです。

## その他

### POSTリクエスト

POSTする場合は、request/4を使用してPOSTメソッドの指定、リクエストボディを指定します。

```erlang
Method = <<"POST">>,
Url = <<"/post_content">>,
Headers = [],
Data = <<"key=value">>
{ok, Client3} = cowboy_client:request(Method, Url, Headrs, Data, Client2),
```

### raw\_request/2

cowboy_clientでは生のHTTPリクエストを送信するraw\_request/2関数が用意されています。
テスト目的でイレギュラーなHTTPリクエストを送信するときに便利です。

```erlang
{ok, Client1} = cowboy_client:init([]),
{ok, Client2} = cowboy_client:connect(cowboy_tcp_transport, "localhost", 8080, Client1),
{ok, Client3} = cowboy_client:raw_request(<<"GET /contents.html HTTP/1.1\r\nHost: localhost\r\n\r\n">>, Client2),
{ok, Status, Headers, Client4} = cowboy_client:response(Client3),
{ok, Body, _Client5} = cowboy_client:response_body(Client4),
```


以上、大雑把なcowboy\_clientの紹介でした。

そもそもcowboyのテストを書くために書かれたモジュールのようなので、
基本的にはHTTP周りのテストを書く際に利用すると良いかと思います。
