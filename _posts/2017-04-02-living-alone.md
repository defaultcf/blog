---
title: "一人暮らしを始めました"
date: "2017-04-02"
layout: "post"
---

[インターン](https://blog.i544c.me/2017/03/29/pixiv-spring-boot-camp-2017/)が終わり、東京から帰ってきてすぐにホモ下宿からアパートに引っ越しました。今の環境をご報告します。

大学には徒歩数分で行ける距離に位置する我が新居。大学に近いということは、本学生なら知っている通り、周りに住居以外何も無いと言うことです。幸い車持ちの友人が同じアパートに住んでいるので、定期的に買い物に連れて行ってもらおうと思います（対価として飯を奢る）。

 

# ネット環境

今までのホモ下宿のネット環境の114,514倍マシです。それにルーター周りを弄れるので遊び甲斐があります。

[![](/assets/img/5621deba8a18b839c7a4321764bb05e8-1.png)](http://test.blog.i544c.me/wp-content/uploads/2017/04/5621deba8a18b839c7a4321764bb05e8-1.png)

自分はRaspberryPi2と3を無駄に持っているので、2をDNSキャッシュサーバに、3をWebサーバにして遊んでいるところです。と言っても、DNSキャッシュサーバの役割は\*.isaac.labをRaspberryPi3に飛ばすだけなんですけどね...

DNSキャッシュサーバには、Unboundを使用しています。

server:
  interface: 0.0.0.0
  access-control: 127.0.0.1/32 allow
  access-control: 192.168.11.0/16 allow
  statistics-interval: 3600

  local-zone: "isaac.lab." redirect
  local-zone: "isaac.lab. A 192.168.11.3"

forward-zone:
  name: "."
  forward-addr: 192.168.11.1

YAMLで書けるし、非常に簡単ですね。

 

WebサーバはNginxを使用し、php7.1-fpmでPHPを動かしています。

server {
  listen 80;
  server\_name isaac.lab isaac.local;
  rewrite ^(.\*)$ http://www.isaac.lab$1 permanent;
}

server {
  listen 80 default\_server;
  listen \[::\]:80 default\_server;

  server\_name ~^(.\*)\\.isaac\\.lab$;
  if ($host ~\* (.\*)\\.isaac\\.lab) {
    set $sub $1;
  }

  location / {
    root /var/www/$sub;
    index index.html index.php index.nginx-debian.html;
    try\_files $uri $uri/ /index.php?$args;
  }

  location ~.php$ {
    root /var/www/$sub;
    try\_files $uri $uri/ =404;
    fastcgi\_pass unix:/var/run/php/php7.1-fpm.sock;
    fastcgi\_index index.php;
    fastcgi\_param SCRIPT\_FILENAME $document\_root$fastcgi\_script\_name;
    include fastcgi\_params;
  }
}

こうすることで、hoge.isaac.labだろうが、huga.isaac.labだろうが、ホスティングしてくれるんですねぇ。ちょっとPHPの実行のためにサブドメインの部分を変数に突っ込むなどしなければなりませんでしたが。

 

こうして、既にいくつかのコンテンツを自室LAN内で公開しています。見たいと思う方は[リプでも飛ばしてください](javascript:tweet();)。 

<script type="text/javascript">function tweet() { window.open("https://twitter.com/intent/tweet?text=%40_leo_isaac%20%E8%A1%8C%E3%81%8D%E3%81%9F%E3%81%84%E3%81%AA💓","Tweet","width=300,height=500,scrollbars=1"); }</script>

www.isaac.lab

Welcome message

info.isaac.lab

phpinfo();

diary.isaac.lab

日記

 

今月はバイトでまたPHPを書くことになりそうなので、ここでPHPの復習でもしますね...
