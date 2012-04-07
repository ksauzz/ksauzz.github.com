---
layout: post
title: "TravisCIによるErlangアプリケーションの自動ビルド"
date: 2012-03-22 20:47
comments: true
categories: erlang
---

ErlangプロジェクトをTravisCIでビルドする手順とビルドステータスの公開方法について。

## TravisCIとは [http://travis-ci.org/](http://travis-ci.org/)

オープンソース向けのCI(Continuous Integration)ホスティングサービスです。
数多くのプログラミング言語がサポートされており、
githubと連携することで、公開しているOSSプロダクトの自動ビルドを手軽に行うことができます。

また、下記データストアを利用したテストも実行可能です。(詳細は省きます)

- MySQL
- PostgreSQL
- SQLite3
- MongoDB
- CouchDB
- Redis
- Riak
- Memcached

## ビルド設定


1. 画面右上の*signin with Github*からTravisCIへログインします。
    Githubへ遷移するので認証を許可します。

    {% img /images/travis_authorizing.png %}

2. profileでビルド対象のリポジトリを選択(ON)します。

    {% img /images/travis_profile.png %}

3. 設定ファイルの用意

    ビルド対象のリポジトリのルートディレクトリに.travis.ymlという設定ファイルを用意します。

    {% gist 2157919 .travis.yml %}
    ここでは以下を設定しています。
    - language: Erlangプロジェクトであることを指定
    - otp_release: ビルド対象のOTPバージョンを指定。(複数可)
    - notifications: ビルド失敗時のメール通知設定

    他言語における設定方法は[こちら](http://about.travis-ci.org/docs/user/languages/)

4. github連携確認
    
    設定が完了したので実際にビルドを起動させます。

    連携したリポジトリでgit push、もしくはGithub Admin画面内のServiceHookでHookTestを実行すると、TravisCIのビルドが起動します。
    ビルドの進捗、結果はTravisCIの画面で確認できます。

    {% img /images/travis_result.png %}
  
以上で設定、動作確認は終了です。

### 実行されるテスト

Erlangプロジェクトで以下のコマンドがデフォルトのテストとして実行されます。
{% codeblock %}
rebar compile && rebar skip_deps=true eunit
{% endcodeblock %}

ルートディレクトリにrebar.configが見つからない場合にはmake testが実行される様です。

{% codeblock %}
make test
{% endcodeblock %}

## READMEファイルでビルドステータスを公開

READMEファイルで最近見かけるビルドステータスです。
適当にREADMEファイルで画像データを読み込むだけで手軽にビルドステータスが公開できます。

{% img /images/travis_build_status_img.png %}

- ビルドステータス取得URL

https://secure.travis-ci.org/*github_id*/*repository_name*.png
{% img /images/travis_build_status.png %}


### 注意事項

- テスト実行時のport bindingはwell-knownportを避けた方が無難。
  11211ポートを使うテストがテストがコケてた。(利用可能なポート番号は特に公開されていない。)

## まとめ

以上、簡単ですがTravisCIの利用方法をまとめてみました。
自動ビルド設定やビルドステータスの公開が、かなり手軽にできるので、
Githubで公開されているプロジェクトにはオススメのサービスです。
