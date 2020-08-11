---
title: "こんにちは、世界。久しぶり、Laravel。"
date: "2016-04-16"
layout: "post"
---

初めまして、Isaacと申します。

何度か他のドメインでWordPressでブログを作ったり消したりを繰り返してきましたが、やっとココで安定しそうです。

このブログを始めた経緯としては、自分の学習の為にVPSを契約したは良いもののVMである事を良い事に色々弄ってはリセットを繰り返していて、継続的なVPS利用をしていないという背景がありまして...じゃあ継続的にVPSを使用し続けるにはどうすれば良いか考えてみたところ、ブログを構築し毎日更新する事を心がければ良いのではないかと、そうなったわけです。

いずれはオリジナルのテーマファイルを作成して、ココをオリジナリティ溢れるブログにしたいですね（希望的観測）。

...といっても何書こうって感じなんですけどね。とりえあえず、今日やったLaravelの設置についてでも書きますか。

 

まずは当方の環境をば。

リモート

サービス

さくらVPS

OS

CentOS

ローカル

ノート

MacBookPro Late2014

OS

OS X || ArchLinux

 

### NginxとPHPのセットアップ

Nginxはyum install nginx で普通に入れて良いですが、PHPをyumで入れようとすると5.3.3が入ります（2016年4月現在）。Laravel5.1はPHP5.5以上じゃないとダメのようなので、PHP5.6を入れます。

rpm -Uvh http://ftp.iij.ad.jp/pub/linux/fedora/epel/6/x86\_64/epel-release-6-8.noarch.rpm

rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-6.rpm

yum install --enablerepo=remi-php56 php php-opcache php-devel php-mbstring php-mcrypt php-mysqlnd php-phpunit-PHPUnit php-pecl-xdebug php-pecl-xhprof

location / {
    root \[お好みでどうぞ\];
    index index.html index.php;
    try\_files $uri $uri/ /index.php?q=$uri&$args;
}

...

location ~ .php$ {
    root           html;
    fastcgi\_pass   127.0.0.1:9000;
    fastcgi\_index  index.php;
    fastcgi\_param  SCRIPT\_FILENAME $document\_root$fastcgi\_script\_name;
    include        fastcgi\_params;
}

display\_errors = On;

...

date.timezone = Asia/Tokyo;

 

### Laravelの設置

VPSにSSH接続して、まずはPHPのパッケージを管理してくれるComposerを入れます。

curl -sS https://getcomposer.org/installer | php

mv composer.phar /usr/local/bin/composer

composer create-project --prefer-dist laravel/laravel \[プロジェクト名\]

これで\[プロジェクト名\]というディレクトリが作られ、その中にLaravel関連のモノが入っているはずです。あとは初期設定をしておきます。

chmod -R 777 storage bootstrap/cache

php artisan key:generate

'debug' => env('APP\_DEBUG', true),

...

'timezone' => 'Asia/Tokyo',

ついでにGit管理下に置いておきます。

git init && git add .

git commit -m "First Impact"

簡単ですね。ローカルからgit clone しておきましょう。このやり方は各自異なるので省略させて頂きます。

ここまで来たら、nginxとphp-fpmを起動して一旦ブラウザで確認してみましょう。シンプルにLaravel5と出たら成功です。

 

いかがでしょうか。Laravelはセットアップが多少面倒というか引っかかりやすいですが、それさえ越えればなかなか頼もしいフレームワークです。この記事をきっかけにLaravelを始めて頂けたら幸いです。

本日の投稿は以上。初の投稿にしては結構書いた気がする...
