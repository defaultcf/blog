---
title: "出会い系(?)を作った"
date: "2018-12-24"
layout: "post"
---

この記事は，FUN Advent calendar 2018の12月25日の記事です．

皆さん！12月25日ですね！恋人のいる方は楽しみ，いない方は唇を噛んで過ごしましたか？？私は男友達と肉を頬張りながら，School Days観てました．

今日はXmas！！25日のAdvent Calendarなので，出会い系サービスを作ることにしました．でも，ただリア充を作るだけではつまらない．どうせなら社会に害為す奴ら同士で潰し合ってもらいたい．

そこで今回作ったのは，迷惑電話を掛ける奴ら同士を繋ぐサービスです．電話やSMSを使うサービスを作るのにピッタリな「Twilio」を使いました．

こんな感じで潰し合ってもらいます．

![](/assets/img/107b209d9d8f2ad9c66ef88c4936054c-1.png)

♥

### 下準備

Twilioは本当に優れたサービスで，自分のアプリケーションと電話周りを繋ぐのを簡単に実装できます．今回のシステムを実装するにあたり，Twilioの以下の機能を使用します．

- Programmable Voice

まずは，Twilioのコンソールにアクセスして，アカウントを作成します．自動的にTrial Accountになるので，制限付きではありますがクレジットが付いてきて，Twilioのサービスを使えるようになります．

次にTwilioで電話番号を入手します．適当に日本の電話番号を入手すると良いでしょう．

  

### 実装

まず2者のうちの一人に電話をかけ，繋がったらもう一者を呼び出して2者を繋ぐような感じで書きます．めっちゃ簡単です．

まずは一人に電話をかけるコードを書きます．

```
from twilio.rest import Client

account_sid = os.environ["ACCOUNT_SID"]
auth_token = os.environ["AUTH_TOKEN"]
client = Client(account_sid, auth_token)

to_number = "+81234567890" # 発信者番号
from_number = "+81123456789" # 発信先

call = client.calls.create(
    url="https://example.com/app.xml", # 後述するTwiMLのURL
    to=to_number,
    from_=from_number,
)

print(call.sid)
```

次に，[TwiML](https://jp.twilio.com/docs/voice/twiml)なるものを使って，もう一者を繋ぐようにコードを書きます．今回作りたいサービスにピッタリの[動詞Dial](https://jp.twilio.com/docs/voice/twiml/dial)を使います．

```
<?xml version version="1.0" encoding="UTF-8"?>
<Response>
  <Dial callerId="+81234567890">
    <Number>+81123456789</Number>
  </Dial>
</Response>
```

このTwiMLは手書きで_https://example.com/twiml.xml_みたいな場所に設置しておいても良いし，Pythonなどで動的に書き出しても良いです．どうせなのでPythonで書きましょう．TwilioのSDKには，TwiMLを書き出す機能もあるのでめっちゃ便利．XMLを手書きで書かなくて済みます．

先程のTwiMLを書き出すPythonのコードは，次のように書けます．

```
from twilio.twiml.voice_response import VoiceResponse, Dial

res = VoiceResponse()
dial = Dial(caller_id="+81234567890") # 発信者番号
dial.number("+81123456789") # 発信先
res.append(dial)

print(res)
```

これをFlaskに組み込んで，_/app.xml_にアクセスしたらこれを返すようにしましょう．コードは省略．

実装は以上．あとは一者に発信するコードを実行すれば，勝手に二人は結ばれます．  

今回作ったコードを実際に使うと，本当に"社会に害為す奴ら"に試すと「営業妨害」で訴えられるかもしれません．なので絶対に使ってはなりません．

こんな感じで電話を使ったサービスを作れるので，皆さんもTwilio使いましょう！以上，FUN Advent Calendar 25日の記事でした．
