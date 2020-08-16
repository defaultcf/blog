---
title: "Docker離れしようと思って、一からサーバーを立てた"
date: "2017-05-09"
layout: "post"
---

最近、自分自身がDockerに依存しすぎたと思うようになりました。

 

Dockerに頼りすぎて一からサーバーを建てるのが億劫になっては嫌だし、ちょうど自分用のRedmineとGiteaを建てようと思っていたので、この際Dockerを使わずにセットアップすることにしました。

サーバーはConoHaで建てました。[このはちゃん最高なので、皆も使おう！](https://www.conoha.jp/referral/?token=kqPEWENqMe.OHzhaZ9HdxAp7Oh_RlkQJ.e_DRHT4E8b8V7M_nXc-1HC)（露骨な宣伝）（コーナーで差をつけろ）

OS

ArchLinux

CPU

1コア

メモリ

512MB

SSD

20GB

OSにArchLinuxを採用した理由としては、格安プランにつきSSDの容量が少ないからですカッコいいからです。

 

## Nginxのセットアップ

今更Apacheなんか使わねぇよ！ということでNginx。

pacman -S nginx

設定ファイルは以下のようにしました。

user isaac;
worker\_processes  1;

events {
    worker\_connections  1024;
}

http {
    include       mime.types;

    sendfile        on;

    keepalive\_timeout  65;

    server {
        listen       80;
        server\_name  localhost;

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        error\_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   /usr/share/nginx/html;
        }
    }

    include /etc/nginx/conf.d/\*.conf;
}

最後のinclude文を書くことで、サービスごとに設定ファイルを書いていく感じにしています。

 

## Redmineのセットアップ

そもそもRedmineとは。

> RedmineはRuby on Railsで開発されている、Webベースのプロジェクト管理ソフトウェアである。(Wikipedia)

RubyやらRailsやらは全く触ったことがないので不安でしたが、[公式のガイド](http://www.redmine.org/projects/redmine/wiki/RedmineInstall)に則って進めれば大丈夫でした。ただ、確か一箇所引っかかる部分があって、Gemfileに書かれているrailsの部分を書き換えないと、DBのmigrate辺りでエラーが出て進めませんでした。多分ArchLinuxで入れたRubyのバージョンが新しすぎるせいです。[参考](http://qiita.com/ironsand/items/8617ba82767884528246)

\- gem "rails", "4.2.7.1"
+ gem "rails", github: "rails/rails", branch: "4-2-stable"

 

Webrickで正常に動作することを確認したら、恒久的に動くようにしましょう。調べてみるとunicornを使うと良いようです。[コチラ](http://qiita.com/noraworld/items/c8acf4bb981c8d8535df)を参考にしました。Socketで動けば、あとはNginxで仲介させるだけです。

upstream unicorn {
  server unix:/home/isaac/redmine/tmp/unicorn.sock;
}

server {
  listen 80;
  server\_name task.i544c.me;
  return 301 https://$host$request\_uri;
}

server {
  listen 443 ssl http2;
  server\_name task.i544c.me;

  ssl\_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl\_certificate /etc/letsencrypt/live/task.i544c.me/fullchain.pem;
  ssl\_certificate\_key /etc/letsencrypt/live/task.i544c.me/privkey.pem;

  error\_log /var/log/nginx/error.log;
  root /home/isaac/redmine/public;
  try\_files $uri/index.html $uri @unicorn;

  location @unicorn {
    proxy\_set\_header X-Real-IP $remote\_addr;
    proxy\_set\_header X-Forwarded-For $proxy\_add\_x\_forwarded\_for;
    proxy\_set\_header Host $http\_host;
    proxy\_pass http://unicorn;
  }
}

これでおｋ。

 

## Giteaのセットアップ

そもそもGiteaとは。

> Gitea は Go で書かれた軽量なコードホスティングサーバーである Gogs のコミュニティによるフォークです。(Arch Wiki)

こちらは、[ArchLinuxのWiki](https://wiki.archlinuxjp.org/index.php/Gitea)を参考にしました。Gitea自体のセットアップは非常に簡単でしたが、gitユーザーのSSH接続に手間取りました...Permission denied (public key) です。権限周りは問題無し、ちゃんと鍵は合っていますし、何なんだろうと思っていたら、答えは非常に単純なものでした。Giteaインストール時に自動で作成されたらしいgitユーザーのホームディレクトリが別のところを向いていたのです...usermod -d /home/git git でホームディレクトリを修正すればよろしい。

あとはNginxを噛ませるだけです。

server {
  listen 80;
  server\_name git.i544c.me;
  return 301 https://$host$request\_uri;
}

server {
  listen 443 ssl http2;
  server\_name git.i544c.me;

  ssl\_protocols TLSv1 TLSv1.1 TLSv1.2;
  ssl\_certificate /etc/letsencrypt/live/git.i544c.me/fullchain.pem;
  ssl\_certificate\_key /etc/letsencrypt/live/git.i544c.me/privkey.pem;

  location / {
    proxy\_pass http://localhost:3000;
  }
}

 

 

そんなこんなで、無事にRedmineの[task.i544c.me](https://task.i544c.me)と、Giteaの[git.i544c.me](https://git.i544c.me)を建てることが出来ました！早速Redmineを使ってVEPします！

 

## 追伸

今回から下👇にリアクションボタンを設置しました！リアクション頂けると励みになるので、是非！
