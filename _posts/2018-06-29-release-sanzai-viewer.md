---
title: "sanzai-viewerをリリースしました"
date: "2018-06-29"
layout: "post"
---

久しぶりに何か作ってみた．

 

この度，私Isaacは『sanzai-viewer』をリリースした．

[https://i544c.me/sanzai-viewer/](https://i544c.me/sanzai-viewer/)

https://twitter.com/\_leo\_isaac/status/1012354992261328897

 

## そもそもsanzai-viewerとは

GitHub上にホスティングされた，散財状況が書かれたCSVファイルをパースし，グラフで分かりやすく表示するウェブアプリ．

使い方はとっても簡単．リポジトリ名を入れて（例: "i544c/sanzai")，エンターを押すだけ．そうするとGitHubのAPIを叩いてCSV探して取ってきてパースし，グラフをChart.jsで描画する．

 

## 作ったきっかけ

ヲタク仲間の[てまー君](https://twitter.com/Tkon_sec)が，東京に旅行に行く際，自身の出費をCSV形式でGitHubに上げていた．自分のプライベートであるはずの散財状況を世界に公開する，というこの行為が私にとってとても面白く感じた．ただCSVのままだと分かりにくいので，グラフ化するWebアプリがあったら便利かな，と思い，この度作成した．

 

## 今後

自分も継続的に[i544c/sanzai](https://github.com/i544c/sanzai)に散財状況を上げていく．

そしてこのsanzai-viewerの機能をもっと上げていきたい．例えば，複数のCSVがある時に選択できるようにしたり，グラフを増やしたい．

 

皆様のIssue, Pull Requestをお待ちしております．
