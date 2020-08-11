---
title: "Django始めてみた"
date: "2016-12-28"
layout: "post"
---

最近Pythonは良いぞってことで色々勉強しているIsaacです。なんで今までこんな素晴らしい言語に触れてこなかったんでしょうね、本当にちゃんと書けばエレガントで美しいコードになるというのに。さらばPHP。

今回はそんな美しいPythonでのWeb開発を加速させるフレームワーク、Djangoのチュートリアルをやった話と、そこで出てきた問題点、その解決方法について書きたいと思います。

* * *

Djangoの学習は公式のドキュメントを見るだけで大体済んでしまいます。何より有志の方が日本語訳してくれてますからね！（最新のバージョンだとまだ翻訳されてないところもあったりする）

[https://docs.djangoproject.com/ja/1.10/](https://docs.djangoproject.com/ja/1.10/)

ここのチュートリアルに沿って学習を進めました。

まず当方の環境としては以下の通り。

OS

MacOS Sierra 10.12.2

VM

Docker for Mac 1.12.5

Djangoコンテナ

python:3.5.2-alpine(87.6MB)

PostgreSQLコンテナ

postgres:9.6.1-alpine(48.16MB)

両方ともAlpineLinuxをベースとしたイメージを使用していますが、おかげで軽い！（AlpineLinux特有の問題にぶち当たることもあります。後述を参照してください）

ソースはコチラ↓

<script src="//cdn.jsdelivr.net/github-cards/latest/widget.js"></script>

チュートリアル1はDjangoコンテナだけで済むので難無く終わりました。問題はチュートリアル2でした。

## 問題1: PostgreSQLに接続出来ない

DjangoはデフォルトでSQLite3、PostgreSQL、MySQL、Oracleに対応していて、自分は最近流行り(?)のPostgreSQLを使おうと思ったのですが、上手いこと動きません。

調べてみたら、Djangoコンテナに必要なライブラリが入っていなかったので、Django用のDockerfileに以下を追記しました。

RUN apk update && \\
    apk add --virtual build-dependencies --no-cache gcc postgresql-dev musl-dev && \\
    pip install psycopg2 && \\
    apk del build-dependencies

 

 

## 問題2: 不正なタイムゾーンだぞ、って言われる

psycopg2.DataError: invalid value for parameter "TimeZone": "UTC"

困惑。UTCとか世界協定時でしょ??なんでや。

と思ったらPostgreSQLコンテナにタイムゾーンのファイルが入ってないらしい。のでPostgreSQL用のDockerfileに次を書き込む。

RUN apk update && \\
    apk add --no-cache tzdata

これでおk。

 

## 問題3: 静的ファイル配信が出来ない

これは謎でしたね。設定ファイルに1行追加する必要があります。

STATIC\_ROOT = os.path.join(BASE\_DIR, 'staticfiles')

これはDjangoに最初から書いてないのが悪い！！←

以上。

 

今後問題が発生したらココに追記していきます。

Django使って色々作ってみたいですねぇ〜〜

 

### **追伸**

全記事の下にリアクションボタンを追加しました！この記事にコメントするほどじゃないけど何かリアクションを残したいと思ったら、是非ボタンを押してください。
