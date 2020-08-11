---
title: "MacにSierraをクリーンインストールした"
date: "2016-10-20"
layout: "post"
---

macOS Sierraが出て1ヶ月が経とうとしている今、大体のソフトは対応しただろうしエラー出ても何かしらの解決策あるだろうということで、どこへ行くにも欠かせない我がMacBook Proちゃんに**クリーンインストール**することにした。その方法と、いつもの環境に戻すまでの道のりを忘備録的な感じでここに残す。

全体の流れとしては、

USBやSDカードなどの外部メディアにSierraを入れる → ブート時に読み込む → ディスクをフォーマット、インストール

って感じになる。

## 何故クリーンインストールなのか

MacやiPhoneなどのApple製品に於いて、既存のOSからそのままアップデートすると大体遅くなる、というのはもはや常識である。それに最近色々と開発しだしたせいで開発環境もファイルもごっちゃになっていて整理したかったからというのもある。

 

## クリーンインストール

まずはApp StoreからSierraのインストールアプリをインストールする。

インストールが完了したら、容量8GB以上のUSBまたはSDカードを挿し、以下のコマンドを叩く。

sudo /Applications/Install\\ macOS\\ Sierra.app/Contents/Resources/createinstallmedia --volume /Volumes/TOSHIBA --applicationpath /Applications/Install\\ macOS\\ Sierra.app --nointeraction

しばらく待って、「書き込んだよ〜」的なメッセージが出ればOK。

完全にバックアップが完了していることを確認して、USBを挿したままOptionキーを押したまま再起動すると、どのディスクから起動するか問われるので、Install macOS Sierraを選択してEnter。

あとはディスクユーティリティで任意のパーティションをフォーマットして、インストールするだけ。

 

## いつもの環境に戻す

ここからが大変。zshやvimみたいにGUIアプリ用のDotfiles作れたら良いのに...

##### ファイアウォール

`設定 → セキュリティとプライバシー → ファイアウォール`でファイアウォールを有効化する。

##### Macの名前変更

`設定 → 共有`でMacの名前変更。ネットワーク上から見られるので、デフォのままだと非常にダサいから忘れずに変えたいところ。

##### Dotfilesから復旧

まだ開発中... [https://github.com/leoisaac/dotfiles](https://github.com/leoisaac/dotfiles)

##### Mac用のパッケージマネージャを入れる

[Homebrew](http://brew.sh/index_ja.html)を入れる。

/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

とりあえずPython3入れる。

`brew install python3`

##### Docker入れとく

[Docker for Mac](https://docs.docker.com/docker-for-mac/)を入れておけば困らない。

##### Pythonの開発環境構築

[Miniconda](http://conda.pydata.org/miniconda.html)を入れとく。

 

* * *

とりあえずこんな感じ。今のところSierraに上げたせいでコレが動かない〜とかは無いが、何かあったらこの記事に追加する。
