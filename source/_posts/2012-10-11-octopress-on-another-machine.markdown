---
layout: post
title: "Octopressを別マシンでセットアップ"
date: 2012-10-11 19:29
comments: true
categories: octopress
---

ご覧のとおり、このブログはOctopressを使っています。
最近、新しいマシンでブログを書こうとしたら、`rake deploy`が上手く動かなかったよという話。
`git clone`しただけではダメみたいです。

ちなみにOctopressのverisionは2.0

#### 手順
1. github上でデフォルトのブランチをsourceにする。
2. `git clone`
3. `rake setup_github_pages`

要するに`rake setup_github_pages`しとけという話です。

１は必須ではないと思いますが、やっとくと楽です。
これで無事デプロイできるようになりました。ってまぁ、このエントリが公開されてることがその証明なわけです。

原因はデプロイ用の`_deploy`ディレクトリがorigin/masterに紐付いてなかったこと。

Rakefileを見ればわかりますが、`setup_github_pages`の役割は元のoctopressのリポジトリをどけるだけでなく、
ローカルリポジトリの`_deploy`ディレクトリを`master`ブランチに関連付ける役割もありました。

まぁ、そんだけです。

### ※追記
どうやらmasterブランチの歴史が書き換わる（初期化される）ので、
それが嫌なら自力で`_deploy`ディレクトリをセットアップするしかなさそうですね。
