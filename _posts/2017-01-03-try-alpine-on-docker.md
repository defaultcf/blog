---
title: "DockerでAlpine動かしてみた"
date: "2017-01-03"
layout: "post"
---

Dockerへの依存が始まった大学の友人と日々を過ごしています、Isaacです。

Dockerは非常に便利な代物ですが、ふと`docker images` してみてDockerのイメージ達が結構大きくなってきていることに、上述した彼も気付く頃だと思います。そのほとんどはOSが占めていることもしばしば...

じゃあ超軽量のOSを使おう！ということで、今回はIsaacお気に入りのArchLinuxよりも軽い、AlpineLinuxをDockerのイメージに使っていく方法を記します。

### AlpineLinuxをお触りしてみる

AlpineLinuxのDockerイメージは、DockerHubに上がっているので、特に理由が無ければそれを使います。

`docker pull alpine`

これだけでalpine:latestが入ります。では試しにこれでコンテナを立ち上げてみます。

`docker run -it --rm alpine ash`

いつもとは違うシェルの入力画面になったかと思います。ashは軽量なUnixシェルらしいです。自分はAlpineLinuxを使って初めて知りました。

 

### パッケージ管理

ArchLinuxにはpacman、Ubuntuにはapt-get、CentOSにはyumがあるように、**AlpineLinuxにはapkがあります**。

DockerでAlpineLinuxを動かした直後はapkのリポジトリが古いというか空（？）なので、まずは`apk update` します。

##### 検索

apk search パッケージ名

##### インストール

apk add --no-cache パッケージ名

`--no-cache` を付けることで、キャッシュが無駄に残ることを防ぎます。

 

 

というか、Ansibleを試したいんですよ。そのためにOpenSSHを入れて、sshdを動かします。

apk add --no-cache openssh openrc
rc-service sshd start

そしたらchrootか何かで外部から叩いてるだろって怒られるので、これ打ったら許すと言われたコマンドを打ちます。

`touch /run/openrc/softlevel`

これで通るようになります。
