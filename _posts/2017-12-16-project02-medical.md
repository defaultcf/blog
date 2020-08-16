---
title: "プロジェクト学習"
date: "2017-12-17"
layout: "post"
---

この記事は、[FUN Advent Calendar 2017 Part 2](https://adventar.org/calendars/2428)の17日目のものです。

 

> 出会い系サイトを作ると言ったな。
>
> あれは嘘だ。

冗談です。プロジェクト学習やってて、そんなの作ってる場合じゃありませんでした。本学名物の"プロジェクト学習"については、[こちらをご参照下さい](https://www.fun.ac.jp/edu_career/project_learning/)。今もそのプロジェクト学習は続いているのですが、同時にドキュメンテーション化も進めていかなければならないので、ついでで記事にすることにしました。

 

## システム概要

自分の班では、生活習慣を正して認知症予防に繋げよう！という感じで食生活記録及び改善を促すシステム「ふーろぐ」の開発を行ってきました。

具体的には、「料理を撮影する箱」と「撮影された写真を管理するサーバ」を構築しました。毎食、食べる前に箱の中に料理を入れることで自動で撮影がされます。この写真はサーバに送られ、撮影者自身がテレビを介して確認できる他、撮影者の家族などがウェブブラウザを介して確認することもできます。

あとは食事の画像解析をして、何の食事であるかを認識し、メニューの偏りなどを識別する機能も実装しました。

...と、色々てんこ盛りなシステムを3ヶ月ほどで作りました。ウェブサイトはコチラ↓

[https://foolog.herokuapp.com](https://foolog.herokuapp.com)

 

## システム構成

食事の撮影を行うカメラはRaspberryPiで作って、Motionというソフトウェアで動体が静止したらサーバーに送るようにしています。

食事の画像を貯めたり、貯まった食事の画像を撮影者の家族が見れるようにするWebサーバは[Heroku](https://www.heroku.com/)にデプロイされた[Ruby on Rails](http://rubyonrails.org/)で動いています。基本的なデータベースにPostgreSQL、ジョブ管理の[ActiveJob](https://railsguides.jp/active_job_basics.html)のためにRedisを使用。アップロードされた食事の画像は[Amazon S3](https://aws.amazon.com/jp/s3/)にアップロードして管理してる。フロントエンドで[Bootstrap4](https://getbootstrap.com/)と（jQueryでDOM操作したくないから）[rails/webpacker](https://github.com/rails/webpacker)で[Vue.js](https://jp.vuejs.org)、あとはフロントエンドでリアルタイムにサーバの情報を反映させるために[ActionCable](https://railsguides.jp/action_cable_overview.html)でWebSocketしてる。

で、RaspberryPiから写真が飛んでくるとOpenCVで皿ごとに写真を分けてdocomo様の画像認識APIに飛ばして料理名を出してる（この画像認識APIでは複数の料理を認識してくれないため）。ってなことを上述したfoologサーバとは別のAPIサーバで動作させてる。これも勿論Heroku。

あとはdocomoから返ってきた料理名と栄養素の組み合わせを先に作っておくために、とあるサイトをスクレイピングしてfixtureとしてデータベースに突っ込んでたりする。（これはそのサイトに利用規約が無かったのでグレー）

まぁリポジトリ見れば早い話だね。

メインのfoologサーバ：[https://github.com/2017pro02/medical-web](https://github.com/2017pro02/medical-web)

カメラ：[https://github.com/2017pro02/medical-camera](https://github.com/2017pro02/medical-camera)

料理名出すAPIサーバ：[https://github.com/2017pro02/medical-cutouter](https://github.com/2017pro02/medical-cutouter)

とあるサイトをスクレイピングしてfixture作成するやつ：[https://github.com/2017pro02/medical-calorie](https://github.com/2017pro02/medical-calorie)

 

## 開発ツール

開発を比較的スムーズに進めるため、積極的にツールを取り入れたつもり。

### Gem

#### [Spring](https://github.com/rails/spring)

Railsに付属している、がデフォルトだと使わない（はず）。これを使うと、rails/rakeコマンドを使うたびに時間がかかるライブラリのロードをバックグラウンドで先にやってくれるようになる。つまり開発が爆速になる。

#### [Foreman](https://github.com/theforeman/foreman)

Herokuでも使われている、簡単に複数のプロセスを走らせるやつ。Procfileに走らせたいプロセスを書いて、bundle exec foreman startするだけ。開発環境でも複数の窓を開いてコマンドを実行する必要がなくなるね。

#### [RuboCop](https://github.com/bbatsov/rubocop)

コード規約を徹底するやつ。実質お一人様開発だったが、自分自身まだ暗黙的なルールが分かっていなかったので、結構直してくれた。設定は勿論onkさんの[OnkCop](https://github.com/onk/onkcop)を使用。

#### [pre-commit](https://github.com/jish/pre-commit)

commit時に問題が無いか確かめて、問題があればcommit時に教えてくれるやつ。上で紹介したRuboCopは勿論のこと、rspecなど色々実行できる。

 

### Chrome Extension

#### [ZenHub](https://chrome.google.com/webstore/detail/zenhub-for-github/ogcgkffhplmphkaahpmffcafajaocjbd)

issueやPRをカンバン上で扱えるようにするやつ。GitHubに違和感無く溶け込むので、超使いやすい。

#### [SessionBox](https://chrome.google.com/webstore/detail/sessionbox-free-multi-log/megbklhjamjbcafknkgmokldgolkdfig)

タブごとにセッションを分けて扱えるようになる。これで複数のアカウントでテストする時でも楽々。

 

### CLI-Tool

#### [anyenv](https://github.com/riywo/anyenv)

rbenvやndenvなどの**envを一括管理してくれるやつ。お陰で.zshrcには**env系は一行しか書く必要が無い。

#### [Docker{,-compose}](https://www.docker.com/)

開発環境を楽に準備するのに導入した。まぁお一人様開発だったが。Railsの実行には使わなかった（マウントしたファイルの読み書き遅いし）、PostgreSQLやRedisのコンテナを利用して、開発機に直にDBを入れるのを回避してた。

 

## 最終的にどこまで実装できたか

基本的な機能は勿論のこと、飛んできた画像を認識して、メニュー名から平均的な栄養価を出して。。。ってところまで。

「**毎日料理を撮影しなきゃならない面倒さを如何に解消するか**」とか、「**続けて撮影したくなるような機能**」とか全然出来てないので、来年度のすうぃふとプロジェクト医療班に期待したい。。。やってくれるか分からないけど。

 

## 最終報告書執筆中

今はこのプロジェクト学習のまとめということで最終報告書を書いてます（2018年1月17日に提出した）。勿論LaTeXで、GitHubのプライベートリポジトリで管理しています（[提出後にパブリックにしました](https://github.com/2017pro02/medical-final-report)）。LaTeX環境を作るのが面倒だったので、[paperist/alpine-texlive-ja](https://hub.docker.com/r/paperist/alpine-texlive-ja/)を使用。またAtomのパッケージ「[on-save](https://atom.io/packages/on-save)」を使ってPDFへの変換を簡略化。超楽。

更にCircleCIを回して表記揺れ検出をして、Reviewdogを使ってPRに修正案をコメントするようにしています。そしてmasterブランチにマージされたらPDFを生成してCircleCIのホストするS3に保存し、Slackに保存先を通知します。

 

良い感じにまとまりませんでした。

明日はPart2では誰も居ませんが、Part1では[chikuwa\_IT](https://adventar.org/calendars/2106#list-2017-12-18)が登録してますねぇ。/m?ruby/のプロです。
