---
title: "nginx-proxyとdnsmasqでローカルでの名前解決"
date: "2019-05-01"
layout: "post"
---

本番に似た環境をローカルに用意する時って、Docker超便利ですよね。なんなら作ったDockerイメージをそのままデプロイできますし。  
dnsmasqとnginx-proxyを使って、名前解決できるようにしました。

## dnsmasqを導入する

`pacman -S dnsmasq`  
ArchLinuxならこれだけでインストールできる！他のOS、Linuxディストリビューションは各々調べて入れてね。

次にdnsmasqを動作させるのですが、`/etc/resolv.conf`を直接書き換えるだけで済む場合と、他のソフトとの兼ね合いで別の設定ファイルに書く場合があります。

### `/etc/resolv.conf`に直接書く

ネームサーバを127.0.0.1にすることで、localhostで動作するdnsmasqで名前解決させるようにする。

```
echo "nameserver 127.0.0.1" > /etc/resolv.conf.head
echo "address=/localnet/127.0.0.1" >> /etc/dnsmasq.conf
systemctl start dnsmasq && systemctl enable dnsmasq
```

これでdnsmasqの準備はok。nginx-proxyの導入に続く。  
更にlocalnetというドメインを127.0.0.1に解決させる。  
NetworkManagerなど/etc/resolv.confを自動で書き換えるソフトを使っている場合は、そのソフトに設定を書くので下を参考に。

### `/etc/resolv.conf`に直接書けない

自分はNetworkManagerというものを使っているので、上述の通り`/etc/resolv.conf`に直接書けません。

```
echo "address=/localnet/127.0.0.1" > /etc/NetworkManager/dnsmasq.d/localnet
cat <<EOF > /etc/NetworkManager/NetworkManager.conf
dns=dnsmasq
EOF
systemctl restart NetworkManager
```

\[main\]

これでNetworkManagerがdnsmasqを起動します。**`systemctl start dnsmasq`などで単体で起動する必要はありません！**

### dnsmasqがちゃんと動いているか確かめる

`dig aaa.localnet`とかやると、最終的に127.0.0.1で解決するか確かめる。

## nginx-proxyを導入する

次にnginx-proxyを導入して、DockerコンテナにVirtual Hostを割り当てる。

[https://github.com/jwilder/nginx-proxy](https://github.com/jwilder/nginx-proxy)

nginx-proxyのREADMEにもある通り、普通は以下のコマンドで動作させる。

```
docker run -d -p 80:80 -v /var/run/docker.sock:/tmp/docker.sock:ro jwilder/nginx-proxy
```

…だけど、複数のプロジェクトのdocker-compose.ymlでやりたいので、この方法じゃダメ。

docker-compose.yml

```
version: "3"
services:
  proxy:
    image: jwilder/nginx-proxy:alpine
    restart: always
    volumes:
      - /var/run/docker.sock:/tmp/docker.sock:ro
    ports:
      - "80:80"

networks:
  default:
    external:
      name: localnet
```

localnetって名前のネットワークに繋ぐ。そしてVirtual Hostを割り当てたいDockerコンテナもlocalnetって名前のネットワークに繋ぐことで、nginx-proxyと通信できる。  
`restart: always`と書いているので、`docker-compose up -d`を実行しておけば、再起動後もnginx-proxyを動かし続けられる。

あとは、whoami.localnetみたいなアドレスで解決したいコンテナを立ち上げる。

```
version: "3"
services:
  app:
    image: jwilder/whoami
    environment:
      - VIRTUAL_HOST=whoami.localnet
    expose:
      - "80"

networks:
  default:
    external:
      name: localnet
```

これで名前解決できるようになった。趣味の開発をする分には最高。

### 注意書き

ただ、localnetで繋ぐと、localnet内のコンテナが相互に参照できるようになる。  
**怪しいイメージから成るコンテナや、コンプラ的にマズいコンテナは繋がない方が良い。**
