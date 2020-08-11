---
title: "VagrantでWindows上に開発環境を作る"
date: "2017-08-01"
layout: "post"
---

私は大学ではMacBookProを、家ではWindowsPC上の仮想環境(ArchLinux)を使って、日々色々と勉強したり開発をしています。 仮想環境は飛ばしてなんぼ（要出典）なので、よく環境構築に時間を費やしてしまいます。一から作り直すとなると楽しいですが時間がかかります。

そこでこの度、Vagrantを使って環境構築をあっという間に済ませるようにしたので、その手順をここに示します。

環境は以下の通りです。

OS

Windows10 Pro (Ver.1073)

VirtualBox

v5.1.26

Vagrant

v1.9.7

VagrantBox

archlinux/archlinux(v2017.07.01)

 

# VirtualBoxとVagrantをインストールする

VirtualBoxはコチラ👉 [https://www.virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads) Vagrantはコチラ👉 [https://www.vagrantup.com/downloads.html](https://www.vagrantup.com/downloads.html)

### ※VagrantBoxの保存先を変えておく

ここは任意です。当方のSSDは残量が残り少ないので、VagrantBoxの保存先をデフォルトのCドライブからHDD3TBのDドライブに変えておきます。 と言っても、環境変数を変えて元のファイルをコピーして再起動するだけです。

export VAGRANT\_HOME=D:\\Documents\\.vagrant.d

元のディレクトリはC:\\Users\\ユーザー名.vagrant.d なので、私はD:\\Documents.vagrant.d にコピーしました。再起動して準備完了。

 

# Vagrantfileとbootstrapを作る

GitHubに上げたので、cloneして使ってください。 [https://github.com/i544c/init-archlinux](https://github.com/i544c/init-archlinux)

 

# 構築する

Vagrantfileのあるディレクトリに移動します。 vagrant up でVagrantfileを読み込み、VagrantBoxの取得から、bootstrapの実行まで行います。vagrant reload で再起動させるとGUI環境が立ち上がり、早速開発できる状態になります。

 

# デスクトップ上にショートカットを置く

一々Vagrant upを唱えたり、VirtualBoxを起動してVMの一覧から起動させなくても良いように、私はデスクトップ上にショートカットを設置しています。

VirtualBoxのVM一覧から今回作成したVMを右クリックして、「デスクトップにショートカットを作成」をクリックすることで出来ます。

作成されたショートカットのプロパティを開いて、リンク先の末尾に「--seamless」とオプションを付与することで、シームレスモードで起動するので超オススメです。

 

# まとめ

これで一々最新のisoを落として、インストールして、環境構築して...という手間が省けます。 折角Windows10 Proを使っているんだし、次はHyper-VとVagrantにしようかな。
