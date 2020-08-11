---
title: "さくらのIoTPlatform触ってみた"
date: "2016-11-14"
layout: "post"
---

11月14日(日)、自分が通っている大学で「さくらのIoT Platformハンズオン」が開催されました。自分も以前Sakura Internetのサーバ見学でその存在を知ってからというもの、ずっと気になっていたので参加できて本当に良かったです。

さて、今回ブログを投稿しようと思ったのは、何もハンズオンの内容を掲載しようと思ったからではありません。某氏のご厚意により、さくらの通信モジュールβとそれのArduinoシールドを手に入れることができたからです！

https://twitter.com/\_leo\_isaac/status/797810901155876864

これが全てです。

"さくらのIoTPlatformを使ったデバイス"とは、kuso-wifi-button（リアル版）を指します。そもそもkuso-wifi-buttonとは、本学のWi-Fiが「クソだ」と思った時に押すデスクトップウィジェット上のボタンです。オープンソースなので、[良ければご覧下さい](https://github.com/uehara1414/kuso-wifi-button)。GUIとして開発していたのですが、実際にボタンだったら面白いよねって話を懇親会で話すと、貰えました。**やったぜ。**

 

頂いた「さくらの通信モジュール（LTE）β版」と「Arduinoシールド」をArduinoとくっつけると、こんな感じ。

[![img_1086](/assets/img/IMG_1086-1.png)](http://test.blog.i544c.me/wp-content/uploads/2016/11/IMG_1086-1.png)

Arduinoと通信モジュール間でI2C通信したいのでジャンパを付けることを忘れないように。（気付けず1時間無駄にしました）

 

今回はkuso-wifi-button(リアル版)を作る前にお試しということで、非常に簡単なものを作ることにします。配線はこんな感じ。

[![wiring](/assets/img/wiring-1.png)](http://test.blog.i544c.me/wp-content/uploads/2016/11/wiring-1.png)

※実際はArduinoの上にシールド・通信モジュールが載っています。

ね、非常に単純なものです。もっと綺麗に配線できるやり方あれば、是非コメントで教えてください...

 

あとはコードを書くだけです。

#include <SakuraIO.h>
SakuraIO\_I2C sakuraio;

const int BUTTON = 2;
boolean buttonState = 0;
boolean buttonStatePre = 1;

uint32\_t count = 0;

void setup() {
  pinMode(BUTTON, INPUT);
  //IoT
  Serial.begin(9600);
  Serial.print("Waiting to come online");
  for(;;) {
    if( (sakuraio.getConnectionStatus() & 0x80) == 0x80 ) break;
    Serial.print(".");
    delay(1000);
  }
  Serial.println("Connect!");
}

void loop() {
  buttonState = digitalRead(BUTTON);
  if(buttonState != buttonStatePre) {
    if(!buttonState) {
      Serial.println(++count);
      sakuraio.enqueueTx(0, count);
      sakuraio.send();
    }
    buttonStatePre = buttonState;
  }
  delay(10);
}

書き終わったら、とりあえずArduinoに書き込んでしまいます。シリアルコンソールを開いてしばらく待って「Connect!」と出てくれば、さくらの閉域網に接続完了です！ボタンを押して、カウントが増えていくことを確認しましょう。

しかし、

**なんて簡単なんだ！**

Arduinoの通信モジュール買って、SIMカード買って、通信の暗号化して、データ受け取るサーバ用意して...

そんなことしなくて良いんです！さくらのIoT通信モジュールとPlatformを利用すれば、そんな些細なことに気を配らず開発が出来るわけです！

 

* * *

...ふぅ。これでローカルでの作業は終了です。続いて手元の通信モジュールから飛んでいるはずのボタン状態を受け取るため、ウェブブラウザから「さくらのIoT Platform β」を触っていきます。

1. [さくらのIoT Platformの開発者向け情報](https://iot.sakura.ad.jp/developer/)からコントロールパネルにログインします。
2. 「新規プロジェクトを作成」から、プロジェクトを任意の名前で作成します。
3. 「モジュール登録」から、任意の名前でモジュールを登録します。ここで必要となる登録用IDとパスワードは、さくらのIoT通信モジュール表面のシールを剥がすと記載されています。
4. 「サービス追加」から連携サービスを追加します。今回はウェブソケットを選択し、任意の名前で追加します。

すると、どうでしょう。受信データの受け取り画面になるわけですが、ボタンを押すと送られてきませんか!?

[![buttontest](/assets/img/buttonTest-1.gif)](http://test.blog.i544c.me/wp-content/uploads/2016/11/buttonTest-1.gif)

ほぼリアルタイムにデータが送られている！

もちろん、このウェブソケットのデータは自前のサーバに送ることができるので、もうkuso-wifi-realButton完成！と言っても過言ではありません。

**素晴らしい、さくらのIoT**

 

 

今回の投稿はここまでとします。今後もkuso-wifi-realButtonに留まらず、「さくらの通信モジュール」および「さくらのIoT Platform」を使って色々と試していこうと思います！

ちなみに今回作成したプログラムなどは[github.com/leoisaac/kuso-wifi-realButton](https://github.com/leoisaac/kuso-wifi-realButton)に上げてますので、ご参照ください。今後も継続的にコミットしていく予定です。プルリク大歓迎。
