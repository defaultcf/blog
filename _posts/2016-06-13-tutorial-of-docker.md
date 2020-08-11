---
title: "Docker始めてみた"
date: "2016-06-13"
layout: "post"
---

最近、VagrantからDockerに浮気しようとしている大学の友人（だが男だ）が、「Docker分かんないよぅ、ふぇぇ」って言っていたのを思い出したので、今回はDockerのインストールから簡単なコンテナの実行までを書きたいと思います。

そもそもDockerとは。

Wikipediaには、

> Linuxのlibcontainerと呼ばれるLinuxコンテナ技術を用いてコンテナ型の仮想化を行う。 - Wikipedia

とあります。とりあえず、VirtualBoxの仮想化とは違うものだと認識しておきます。

 

## インストール

早速、Dockerをインストールしてみましょう。

##### Macの場合

Docker for Macというアプリケーションさえあれば、Dockerが使えるようになります。

1. [公式ページ](https://docs.docker.com/docker-for-mac/)から[dmgファイル](https://download.docker.com/mac/stable/Docker.dmg)をダウンロード。
2. dmgファイルをマウントして、アプリケーションディレクトリにドラッグ&ドロップ。
3. あとは表示される手順に従う。

これだけで、Mac起動時にDockerが自動起動します。以前は**Docker Toolbox**というものしかなくて起動がちょっと面倒でしたが、**Docker for Mac**なら起動してすぐにでもdockerコマンドやdocker-composeコマンドを叩けるので最高です。

##### Linuxの場合

各ディストリビューションによってコマンドは変わりますが、概ね同じはずです。ここではArchLinuxを例にインストールの手順を示します。

pacman -S docker
gpasswd -a ${username} docker
systemctl enable docker

以上のコマンドを打った後、再起動。再起動後にdocker infoでバーッと情報が出てきたらインストール成功です。

##### Windowsの場合

「えーWindowsでDocker動かすのー？」「WindowsでDocker動かすなんて小学生までだよねー」

...

というのは冗談ですが、先述の通りDockerはLinuxの技術を用いて動作するので、少なくともVirtualBoxなどでUNIX環境の準備が必要となります。

面倒くさくないですか??ArchLinuxを入れれば万事解決です。さぁArchLinuxを入れよう。

 

## Dockerでハローワールド

docker run -it --rm ubuntu:latest /bin/echo 'Hello, world'

ローカルにubuntu:latestイメージが無ければ取ってきて、あればそれを使ってコンテナを立ち上げてechoコマンドを実行します。`--rm`を付けているので、出力が終わったらコンテナは削除されます。簡単ですね。

...自分は簡単だとは思いません。1つのコンテナを立ち上げるのに、ターミナルの幅一杯のコマンドを打たなきゃならないなんて、面倒過ぎます。しかもこれが複数のコンテナを使ったサービスとなると、デプロイする度にコンテナの数だけコマンド打たなきゃなりませんし、止める時も同様... やってられません。

例)

docker run -it -d -p 3306:3306 --name mysql -e MYSQL\_ROOT\_PASSWORD=pass mysql:latest
docker run -it -d -p 80:80 --name centos --link mysql centos:latest

停止は`docker stop mysql centos`

削除は`docker rm mysql centos`

↓↓↓↓↓↓↓↓↓↓

## そんな時こそ、docker-compose

Docker for Macにはdocker-composeが入っているので、そのまま使えます。Linuxの場合は別途`pacman -S docker-compose`するなりして入れる必要があります。

centos:
    image: centos:latest
    name: centos
    links:
        - mysql:mysql

mysql:
    image: mysql:latest
    name: mysql
    environment:
        - MYSQL\_ROOT\_PASSWORD: pass

以上のファイルを任意のディレクトリに設置し、そのディレクトのところで`docker-compose up -d`を叩くと、docker-compose.ymlに沿ってコンテナが生成されます。

停止は`docker-compose stop`

削除は`docker-compose rm`

ね、簡単でしょ??

 

## コンテナの入り方

docker exec -it centos bash

こんだけでcentosコンテナにbashで入れます。またこんなことも↓

docker exec -it centos bash yum install -y mysql
docker exec -it centos　mysql -u root -p -h mysql -P 3306

MySQLに入れちゃいます。

 

* * *

大学の友人U君には是非Docker沼にハマってほしい。1コマンド試すだけでもDocker動かすくらいには。
