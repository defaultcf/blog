---
title: "大学生活でLinuxを使う"
date: "2019-12-12"
layout: "post"
---

この記事は、[FUN part2 Advent calendar 2019](https://adventar.org/calendars/4179)の12月12日の記事です。

### 誰

[https://scrapbox.io/i544c/i544c](https://scrapbox.io/i544c/i544c)

公立はこだて未来大学複雑系知能学科知能システムコース4年の者です。学部3年を2回やっていて、学部4年をあと1回します。みんなは、朝ちゃんと起きて数学をやろうな。

で、私は大学でも仕事でも私生活でも、Arch Linuxを使っています。その良さを今回は書きます。

### 何故

何故Linuxを使うのか。それは、隅から隅までカスタマイズしたいから。

だって、文句言いながらも使い続けるしかないって、不自由極まりない。Windowsだとアップデートが面倒だし、MacOSだと最近は深刻なバグの話をよく聞く。

だったら、自分の好きな筐体に好きなOSをインストールすれば良い。

私の場合は学部1年の時にLinuxに出会い、特にArch Linuxを気に入って、[当時持ってたMacBook Proに入れてデュアルブートしたり](https://blog.i544c.me/2016/04/20/install-archlinux-on-macbookpro/)した。その後、ThinkPadの良さに取り憑かれて、[ThinkPad T480sを買ってArchLinuxを入れて](https://scrapbox.io/i544c/%E7%92%B0%E5%A2%83%E6%A7%8B%E7%AF%89)、今使っている。これが最高なんだ。

### どのように

どのようにインストールし、どのように使うか。殆どは[ArchWiki](https://wiki.archlinux.jp/)に書いてある。

[https://wiki.archlinux.jp/](https://wiki.archlinux.jp/)

他のLinuxディストリビューションを使っていても、このサイトを参考にしたことのある人はそれなりに居るのではないだろうか。ソフトウェア名でググったりしているとよくArchWikiに出逢うと思う。

それはArchWikiが巨大だから。ニッチなソフトウェアでも載っていたりする。みんなも還元していこうな。

### WindowsやMacOSと比較する

WindowsやMacでしかできないことって、実は殆ど無い。WordやExcelのファイルは、Web版のWord、Excelで大体済む。Webの時代最高。

特に顕著にLinuxの良さを感じるのは、Dockerを利用した時。volumeを使用した時のIO周りは、どうしてもMacだと遅くなる。

Linuxでできないのは、

- Microsoft OfficeでWeb版と互換性の無い機能を使う時
- iOS用のアプリケーションをビルドする時

くらい。

### まとめ

というわけで、みんなもLinuxユーザになろう。

### おまけ

[https://fun-oauth.i544c.me](https://fun-oauth.i544c.me)

未来大生用のOAuthサービスを作って動かしています。ここでユーザが未来大生であることを確認しているので、サービスに組み込むことでユーザを未来大生だけに制限できます。

[http://adfun3m3yz3qlkvbkfjdbe4womooqceca4iffmnm2kxulgvao5wfcvyd.onion/](http://adfun3m3yz3qlkvbkfjdbe4womooqceca4iffmnm2kxulgvao5wfcvyd.onion/)

サンプルのアプリケーションも作りました。未来大生向けの匿名日記サービスです。今のところ荒れていませんが、ヤバくなってきたら原因のユーザをBANしますし、場合によってはサービスごと止めます。

本当はAdvent CalendarでOAuth2の話をしようとしたけれど、そんな話は巷に溢れているので変えました。
