---
title: "Vagrantで開発を加速させる(加速するとは言ってない)"
date: "2016-06-16"
layout: "post"
---

最近、大学の友人がVagrant良いぞぉって言うものだから初めてみた。

そもそもVagrantは、VirtualBoxを介して仮想環境をより簡単に構築できるようにしたものである。いちいちGUI操作して仮想環境を立ち上げて...ってやらなくても良いからめっちゃ楽。

当方は勿論ArchLinuxを使っているので、pacman -S vagrant でインストール出来る。なお、VagrantはVirtualBoxを使ったツールなので、まだVirtualBoxを入れていなければpacman -S virtualbox で入れる。

では、早速vagrant init でカレントディレクトリにVagrantfileという設定ファイルを作成する。といっても生成されるこのファイルはほとんどがコメントで、正直vagrant init せずとも以下の内容のファイルをVagrantfileの名で作っておいた方が楽だったりする。

Vagrant.configure("2") do |config|
    config.vm.box = "hashcorp/precise64"
end

Vagrantfileの作成が終わったら、vagrant up で起動させる。ここでエラーが出ると思われる。この指示に従ってVBoxManage --version を実行する。するとまた何かしらエラーが出るので、また指示に従ってsudo /sbin/rcvboxrdv setup を実行する。これで何もエラーが出なければ良いが、何かしらロード出来ていないと出たらpacman -S linux-headers する。もっかいsudo /sbin/rcvboxrdv setupすればちゃんとロード出来ているはず。

これでやっとvagrant up 出来る。バーッと流れていってエラー出ずにコマンド入力待機に戻ったら成功。vagrant ssh で仮想環境に接続出来るし、停止させる時はvagrant halt 、再開させる時はvagrant up 、作成した仮想環境を削除する時はvagrant destroy すれば良い（Vagrantfileは削除されないのでまたvagrant up で仮想環境を作成出来る）。

* * *

まぁ他にも仮想環境作成時にシェルスクリプトを実行させるようにしたり色々できるが、LAMP環境構築するのはなかなか骨が折れる...。そんな中、ScotchBoxなるものを見つけた。

[https://box.scotch.io/](https://box.scotch.io/)

こいつをgit cloneで落としてvagrant upさえしてやればLAMP環境を備えた仮想環境が出来ているという優れもの。その前にvagrant plugin install vagrant-hostsupdater した上にpacman -S net-tools して再起動しておく。

 

いやー便利だ。
