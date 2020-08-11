---
title: "VPSのセットアップ"
date: "2016-06-24"
layout: "post"
---

最近、自分はDockerにハマっていて、[GitHub](https://github.com/LeoIsaac)の方にいくつかコンテナを立ち上げるためのスクリプトを上げたりしている。だってホストの環境に依存せずにデプロイ出来るんですよ？？最高じゃん。

とは言っても、当然ホスト側の整備は必要なので、今回はVPSのセットアップをおさらいしておく。

なお、当方の環境は以下の通り。

VPS

ConoHa

OS

ArchLinux

## セットアップ

プランを選択し、サーバーが立ち上がったら、とりあえずローカルからパスワード認証でSSH接続し、以下の感じでセットアップしていく。

export TERM=linux

sed -i -e "s/#en\_US.UTF-8/en\_US.UTF-8/" /etc/locale.gen

sed -i -e "s/#ja\_JP.UTF-8/ja\_JP.UTF-8/" /etc/locale.gen

locale-gen

echo LANG=en\_US.UTF-8 > /etc/locale.conf

export LANG=en\_US.UTF-8

ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

# いつも使うユーザーを作成
useradd -m -G wheel {user}

passwd {user}

visudo
# env\_keep = "Home"と%wheel ALL=(ALL) ALLのコメントアウトを外す

# 以降は一般ユーザーで作業する
su - {user}

 

## 認証方法の変更

ローカルでもう一つ端末を開き、ssh-keygenで公開鍵と秘密鍵を生成する。うち公開鍵の方（.pubが付いている方）をscpでサーバーに送る。送った公開鍵を、いつも使うユーザーのホームディレクトリ下に新たに.sshというディレクトリを作成し、そこにauthorized\_keysの名で入れる。今後他の端末からアクセスするようになった時、公開鍵は随時authorized\_keysに追加していくことになる。

chmod 700 ~/.ssh

chmod 600 ~/.ssh/authorized\_keys

最後に一旦rootになって、

sed -i -e "s/#Port 22/Port 8020/" /etc/ssh/sshd\_config

sed -i -e "s/PermitRootLogin yes/PermitRootLogin no/" /etc/ssh/sshd\_config

sed -i -e "s/#PubkeyAuthentication yes/PubkeyAuthentication/" /etc/ssh/sshd\_config

systemctl daemon-reload

systemctl reload sshd

これでセキュリティも向上した。（完全に安全とは言っていない）
