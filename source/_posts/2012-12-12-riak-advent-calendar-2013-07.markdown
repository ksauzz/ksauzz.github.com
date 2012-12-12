---
layout: post
title: "ひとりでやる(?)Riak Advent Calendar 2012 day7 - Ruby Clientで遊んでみよう "
date: 2012-12-12 23:29
comments: true
categories: riak ruby objectmapper
---

ということでRiakのRubyライブラリをご紹介。RiakのProtocol Buffer/HTTPクライアントであるriak-ruby-clientとObject MapperであるRippleの２本立てです。Rippleに関してはRailsへの導入方法を簡単に説明します。

* riak-ruby-clientの使い方
* RailsへRippleを導入する

## [riak-ruby-client](https://github.com/basho/riak-ruby-client)の使い方

### インストール

gemでサクッとインストールします。

```
% gem install riak-client
Successfully installed riak-client-1.1.0
1 gem installed
```

### 利用方法

#### 1. Clientの生成

Procotol Bufferを使うか、HTTPを使うかをnewするときに指定できます。

##### 1.1. HTTP

```ruby
require 'riak'
client = Riak::Client.new
```

##### 1.2. Protocol Buffer

```ruby
require 'riak'
client = Riak::Client.new(:protocol => "pbc")
```

##### 1.3. 複数ノードへの接続

```ruby
require 'riak'
client = Riak::Client.new(:nodes => [
  {:host => '10.0.0.1'},
  {:host => '10.0.0.2', :pb_port => 7070},
  {:host => '10.0.0.3', :http_port => 8080}
])
```

#### 2. Objectを保存する

生成したclientを使ってObjectを保存します。
やり方が３パターンほどあるので、それぞれサンプルをご紹介。
Riak::RObjectの生成方法が違うだけでやっていることは同じです。

##### 2.1 シンプルに使う

```ruby
bucket = client.bucket("doc")
object = bucket.get_or_new("index.html")
object.raw_data = "<html><body>Hello, world!</body></html>"
object.content_type = "text/html"
object.store
```

##### 2.2 Riak::RObjectを明示的に利用

```ruby
bucket = client.bucket("doc")
new_one = Riak::RObject.new(bucket, "index2.html")
object.raw_data = "<html><body>Hello, world!</body></html>"
object.content_type = "text/html"
object.store
```

##### 2.3 ハッシュを使って

```ruby
object = client['doc']['index.html']
object.raw_data = "<html><body>Hello, world!</body></html>"
object.content_type = "text/html"
object.store
```

保存したデータを[httpie](https://github.com/jkbr/httpie)で確認してみます。

```
% http localhost:8098/buckets/doc/keys/index.html
HTTP/1.1 200 OK
Content-Length: 39
Content-Type: text/html; charset=UTF-8
Date: Wed, 12 Dec 2012 16:55:44 GMT
ETag: "44tAJLzAUJTa20TtR8s5fJ"
Last-Modified: Wed, 12 Dec 2012 14:53:48 GMT
Link: </buckets/doc>; rel="up"
Server: MochiWeb/1.1 WebMachine/1.9.0 (someone had painted it blue)
Vary: Accept-Encoding
X-Riak-Vclock: a85hYGBgzGDKBVIcypz/fgacmLY0gymRKY+VoUbM9hRfFgA=

<html><body>Hello, world!</body></html>
```

はい、確認できました。
簡単ですがriak-ruby-clientの紹介はここまで。

## RailsへRippleを導入する

[Ripple](https://github.com/basho/ripple) - A rich Ruby modeling layer for Riak -

RippleはRiakのデータをオブジェクトにマッピングしてくれるライブラリです。ActiveRecordライクに利用できます。
Riakを使ってアプリケーションを作る際には、基本的にRippleやそれに類似したライブラリを使うことになる筈です。

それではRailsへRippleを導入する方法を簡単に紹介します。

なお、このサンプルではRails 3.2.8を使用しています。

#### 1. Railsアプリケーションの生成

`rails new`でアプリケーションを生成します

```
% rails new ripple-example
      create
      create  README.rdoc
      create  Rakefile
      create  config.ru
...
% cd ripple-example
```

#### 2. rippleをdependenciesへ追加

Gemfileに以下を追加します。
Railsの3.2.8を使っているのでrippleは1.0.0.beta2を使います。

```
gem "ripple", "~>1.0.0.beta2"
```

bundle installします。

```
bundle install
```

#### 3. セットアップ

`rails g ripple`します。

```
% rails g ripple
      create  config/ripple.yml
      create  app/mapreduce
      create  app/mapreduce/contrib.js
      create  app/mapreduce/iso8601.js
      create  app/mapreduce/ripple.js
      insert  test/test_helper.rb
      insert  test/test_helper.rb
```

config/ripple.ymlを編集してRiakの設定を自分の環境に合わせます。
sourceにはローカルのriakコマンドがインストールされているパスを設定してください。

```
development:
  http_port: 8098
  pb_port: 8087
  host: 127.0.0.1
  source: /usr/local/bin  #Default for Homebrew.
```

それでは試しにmodelを生成します。
propertiesとしてname, ageを指定してみました。

```
% rails g ripple:model User name:string age:integer
      create  app/models/user.rb
      invoke  test_unit
      create    test/unit/user_test.rb
      create    test/fixtures/users.yml
```

生成したModelはこんな感じになります。

```ruby
class User
  include Ripple::Document

  property :name, String
  property :age, Integer
end

```

#### 4. 動作確認

consoleで動作確認してみます。
まずはデータを保存。

```
% rails console
Loading development environment (Rails 3.2.8)
irb(main):001:0> user = User.new
=> <User:[new] name=nil age=nil>
irb(main):002:0> user.name = 'たろう'
=> "たろう"
irb(main):003:0> user.age = 18
=> 18
irb(main):004:0> user.save
=> true
irb(main):005:0> user.key
=> "QB4nWCL1SpvCe5csclOUgmNX7XN"
```

保存後に取得したkeyの値がオブジェクトのIDとなります。
このIDを使ってデータが保存されたか確認してみます。

```
irb(main):006:0> User.find "QB4nWCL1SpvCe5csclOUgmNX7XN"
=> <User:QB4nWCL1SpvCe5csclOUgmNX7XN name="たろう" age=18>
```

はい、保存されていますね。

なお、オブジェクトのID(riakのkey名)は、以下の`key\_on`をModel内で指定する事により、
自動生成された値でなく、特定のproperyを使用することができます。
下記の例だとkey\_nameというpropertyがriakのkey名に使われます。

```
key_on :key_name # use :key_name as key name.
```

こんな感じでRiak+Railsのアプリケーションを作って遊んでみてはいかがでしょうか。
今日はここまで。
