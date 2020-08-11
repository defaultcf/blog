---
title: "近況報告と、TOMOYO Linuxを導入しようとしている話"
date: "2020-05-16"
layout: "post"
---

2年越しにやっと就活をしているが、10社くらい受けて心折れそうになっている。自分のやりたいことって何だっけ...

[https://scrapbox.io/i544c/就活](https://scrapbox.io/i544c/就活)

最近、note.comを使い始めた。WordPressで公開しているこのブログの場合、RSSで更新をチェックしている奇特な人か、Google検索でヒットするかでしか見てくれない。その点、note.comではサービス内でのフォロー機能や検索、ハッシュタグからも来てくれる。

WISYWIGはシンプルで、語るぞ！って時に向いているように思うので、今後ヲタクな話はnote.comに書く予定。 [https://note.com/i544c](https://note.com/i544c)

早速、個人で開発したウェブサービスのリリースノートを書いてみた。

[https://note.com/i544c/n/n7153c99645b2](https://note.com/i544c/n/n7153c99645b2)

## TOMOYO Linux

ずっとSELinuxを導入しようと奮闘してきたが、幾つかビルドに失敗するものがあって、辛かった。ビルドに必要なライブラリ、ツールが分からんし、ちゃんとバージョンを指定してほしい。

**\[2020年6月11日更新\]** 試行錯誤した末、必要なパッケージが分かってビルドに成功した。 [https://scrapbox.io/i544c/SELinux](https://scrapbox.io/i544c/SELinux)

そんな中、TOMOYO Linuxを知ったので試すことにした。

[https://scrapbox.io/i544c/TOMOYO\_Linux](https://scrapbox.io/i544c/TOMOYO_Linux)

（調べて分かったことなどは [https://scrapbox.io/i544c](https://scrapbox.io/i544c) に書くようにしている）

VirtualBoxで環境を作って、色々試してみた。ポリシーをTUIで操作できるのが気持ちいい。

試しにEmacsのプロセスをポリシー無しでenforce状態にしてみる。

![](/assets/img/Screenshot_2020-05-16_23-14-16.png)

※Emacsに恨みはありません

これは単純な例だが、例えば特定のディレクトリ内での編集しか許可しないとか、ネットワーク通信を遮断するということもできる。

特に有益な使い方としては、Apacheの設定ミスを防ぐために、Apacheのプロセスが/var/www/html以外にアクセスすることを拒否するというのがある。（起動に必要なファイルは許可する）。つまり、ディレクトリトラバーサルを防げる。また、Apacheのプロセスから指定したプロセス以外を起動させないことができるので、何かCGIなどで動作しているプログラムに脆弱性があったとしても、コマンドインジェクションを起こさない。

もっとドキュメントを読み進めて、日常的に使うようになりたい。
