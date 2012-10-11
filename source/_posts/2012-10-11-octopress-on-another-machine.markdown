---
layout: post
title: "Octopressブログの別マシンにおけるセットアップ手順"
date: 2012-10-11 19:29
comments: true
categories: octopress
---

わかるとは思いますが、このブログはOctopressで作られています。
最近、新しいマシンでブログを書こうとしたら、`rake deploy`が上手く動かなかったよという話。
`git clone`しただけではダメみたいです。

ちなみにOctopressのverisionは2.0

#### 手順
1. github上でデフォルトのブランチをsourceにする。
2. `git clone`
3. `rake setup_github_pages`

要するに`rake setup_github_pages`しとけという話です。

１は必須ではないと思いますが、やっとくと楽でしょう。
これで無事デプロイできるようになりました。ってまぁ、このエントリが公開されてることがその証明なわけです。

原因はデプロイ用の`_deploy`ディレクトリがorigin/masterに紐付いてなかったこと。

Rakefileを見ればわかりますが、`setup_github_pages`の役割は元のoctopressのリポジトリをどけるだけでなく、
ローカルリポジトリの`_deploy`ディレクトリを`master`ブランチに関連付ける役割もあったということです。

まぁ、そんだけです。
