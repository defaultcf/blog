---
title: "色々やったけど、とりあえずドメイン変わったよ"
date: "2016-10-13"
layout: "post"
---

leo-isaac.net/blogとか、lab.leo-isaac.netとか、leoisaac.github.ioとか、色々管理することになってしまっていてごっちゃになっていたので、一旦整理することにしました。

 

### 1\. ドメインを取得しました。

[leo-isaac.net](https://leo-isaac.net)ってなんか長くね??ということで、より短いドメインi544c.meを取得しました。[お名前.com](http://onamae.com)にて1年間税込929円で.meドメインが売っていたので、あれ安くね!?と思い、買いました。

 

### 2\. ブログのドメイン移行

もうお気付きかもしれませんが、**ブログの設置場所を[leo-isaac.net/blog](http://leo-isaac.net/blog)から[blog.i544c.me](https://blog.i544c.me)に変更しました**。

またそれに伴い、leo-isaac.netにアクセスするとi544c.meへ、leo-isaac.net/blog/以下にアクセスするとblog.i544c.me/以下へ、いずれも301で遷移するように`.htaccess`を書きました。あと半年はleo-isaac.netが有効なので、それまでi544c.meに飛ばし続けてもらいます（そうするとleo-isaac.netに対する今までのGoogleの評価がi544c.meに移るらしい）。

ちなみにleoisaac.github.ioとi544c.meを紐付けています。

 

### 3\. Let's EncryptでTLS対応

今までは試験的に運用していたVPS上のNginxでブログを運用してきましたが、この度レンタルサーバに移行しました。VPSだったのでLet's Encryptの適用も簡単でしたが、レンタルサーバだとLet's Encryptの鍵取得も面倒になってしまいました...。いつか自動化します。

 

### 4\. Google Analyticsで見える化

今までWordPressのプラグインJetpackで記事毎のアクセス数だけカウントしていましたが、Google Analyticsを導入したので訪問者の動きがより鮮明になる（はず）。

 

* * *

それぞれの詳細は後日、別途記事に書く予定。
