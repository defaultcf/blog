---
title: "Firefoxにて、window.createで出すwindowのlocation barを消す"
date: "2019-07-01"
layout: "post"
---

だいぶニッチな話。

## 2020年7月7日、追記

ここで紹介している方法は使えなくなった。JavaScriptを使ってブラウザのUIを変更できるなんてけしからん、ということらしい。

[https://developer.mozilla.org/en-US/docs/Web/API/Window/open#Popup\_condition](https://developer.mozilla.org/en-US/docs/Web/API/Window/open#Popup_condition)

下記以降の当時の文章は一応そのまま残しておく。

まず始めに、自分は現時点でstableなFirefox67.0.4を使っていて、Picture in Pictureが使えない。使えるようになったら、こんなことしなくて済む。Firefox68から、設定を弄ることでPicture in Pictureが使えるようになるらしい。(ただ相変わらずニコ動では機能しない)

自分はYoutubeで動画を観る時は、FirefoxでYoutubeの再生画面を出し、[このスクリプト](https://greasyfork.org/ja/scripts/811-resize-yt-to-window-size)でwindow内で動画を最大化して、Xfceのウィンドウオプションで『最前面』・『全てのワークスペースで表示』としている。

![](/assets/img/show_location_bar.png)

location barあり

この時にlocation barが邪魔だなぁ、とずっと思ってきた。そこで今回はこのlocation barを消す取り組みをしたので、ここに記す。

### せっかちな君へ

先に結論を記す。

1. Bookmarkletを作成する
2. about:configでdom.disable\_window\_open\_feature.locationをfalseにする

Bookmarkletはこんなの。

```
javascript:(() => {window.open(window.location.href, window.document.title, "menubar=no,location=no,resizable=yes,scrollbars=yes,status=no")})();
```

これで、任意のページでこのBookmarkletをクリックすれば、location bar無しのウィンドウでそのページが開く。

![](/assets/img/hide_location_bar.png)

location barなし

これで最高になった。

### 足取り

まずは先駆者が居ないか調べた。『Firefox location bar hide』とかでググれば、色んな手法が出てくる。その中でも良い感じに回答が付いているものがあった。

[https://superuser.com/questions/977912/firefox-hide-everything-except-content-area-of-the-browser](https://superuser.com/questions/977912/firefox-hide-everything-except-content-area-of-the-browser)

ここの通り、最初に既存のAddonを試した。Popupとして最前面に出すものなら幾つかある。

- [https://addons.mozilla.org/en-US/firefox/addon/new-window-without-toolbar/](https://addons.mozilla.org/en-US/firefox/addon/new-window-without-toolbar/)
- [https://addons.mozilla.org/en-GB/firefox/addon/popup-window/](https://addons.mozilla.org/en-GB/firefox/addon/popup-window/)

ただ自分の環境では『全てのワークスペースで表示』ができなかった。多分親のウィンドウに依存していて、子のウィンドウで個別に『最前面』とか『全てのワークスペースで表示』ができないのだろう。

次にuserChrome.cssを使ったウィンドウレイアウトの修正を考えたが、今回は特定のウィンドウでのみlocation barを消したいので、これは止めた。

最後にbookmarkletを試した。先述した記事に書いてあるものだけだとlocation barが出てきてしまうので、about:configを眺めて、dom.disable\_window\_open\_feature.locationを見つけてfalseにした。結果、上手くいった。
