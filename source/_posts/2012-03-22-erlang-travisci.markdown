---
layout: post
title: "ErlangとTravisCIの連携"
date: 2012-03-22 20:47
comments: true
categories:
---

## TravisCIとは (http://travis-ci.org/)

オープンソース向けのCI(Continuous Integration)ホスティングサービスです。
数多くのプログラミング言語がサポートされており、
githubと連携することで、公開しているOSSプロダクトの自動ビルドを手軽に行うことができます。

また、データストア(下記)を利用したテストも実行可能です。(詳細は省きます)

- MySQL
- PostgreSQL
- SQLite3
- MongoDB
- CouchDB
- Redis
- Riak
- Memcached

## 連携手順 


1. *signin with Github*からTravisCIへログインします。

    {% img /images/travis_authorizing.png %}

2. profileでビルド対象のリポジトリを選択(ON)します。

    {% img /images/travis_profile.png %}

3. 設定ファイルの用意

    ビルド対象のリポジトリのルートディレクトリに.travis.ymlという設定ファイルを用意します。

    設定内容
    - language: erlangビルドであることを指定
    - otp_release: ビルド対象のOTPバージョンを指定
    - notifications: メール通知設定

    {% gist 2157919 .travis.yml %}

    デフォルトでは以下のテストで実行されます。
    {% codeblock %}
    rebar compile && rebar skip_deps=true eunit
    {% endcodeblock %}
    ルートディレクトリにrebar.configが見つからない場合にはmake testが実行される様です。
    {% codeblock %}
    make test
    {% endcodeblock %}

4. 連携確認

    ビルド対象のリポジトリでgit pushするか、Github Admin画面内のServiceHookでHookTestを実行する。

    {% img /images/travis_result.png %}

5. READMEファイルにビルドステータスを表示

    ビルドステータスを画像データで取得することができます。
    https://secure.travis-ci.org/*github_id*/*repository_name*.png

    {% img /images/travis_build_status.png %}

    READMEファイルで最近見かけるこれです↓
    適当にREADMEファイルで読み込みましょう。

    {% img /images/travis_build_status_img.png %}

### 注意事項

- テスト実行時のport bindingはwell-knownportを避けた方が無難。
  11211ポートを使うテストがテストがコケてた。利用可能なポート番号は特に公開されていない。

## まとめ

以上、簡単ですがTravisCIの利用方法をまとめてみました。
自動ビルド設定やビルドステータスの公開が、かなり手軽にできるのでTravisCIはオススメです。
