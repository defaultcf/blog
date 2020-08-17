---
layout: post
title:  "Welcome to Jekyll!"
date:   2020-08-11 23:52:48 +0900
categories: jekyll update
---

今までWordPressで運用してきたこの blog.i544c.me を、Jekyllでの生成に移行しました:clap:

## 理由
移行した理由は...
- リッチなエディタに馴染めなかった
  - やっぱりMarkdownで書きたい
- VPSでの運用コスト
  - 無料のサービスを利用することで金銭的コストから開放される
- セキュリティ上のリスク
  - 静的にページ生成すりゃ良いじゃん

Jekyllを選んだのは、GitHubでサクッと公開できるから。


## 手段
WordPressに標準搭載されているエクスポートツールを使って、「すべてのコンテンツ」をXMLファイルとして得る。

次にこのXMLファイルからMarkdownファイルに変換する。

[github.com/lonekorean/wordpress-export-to-markdown](https://github.com/lonekorean/wordpress-export-to-markdown/)

このツールを使うことで、Markdownファイルに変換できる他、更に記事に埋め込んだ画像なんかも取ってきてリンクし直してくれる（！）。ただ画像のリンクが理想の場所にならなかったので、自分でちょっとコードを変更して実行した。

あとは、Jekyllのプロジェクトを作成して、Markdownファイル達を設置して、完了。


...とはならなかった。一部ページのURLが、WordPressの頃のものと違うものになっていた。
- URLに2バイト文字が含まれる場合
- 記事作成日と公開日が異なる場合

上記の条件に当てはまる時に、リンクが元のと異なることが分かった。新たなURLでやっていくのも良いが、なるべくパスはそのままにしておきたい。リンクとかされていたら困ることになるし（まぁリンクされてないと思うが）。

問題の記事は10も無かったので、JekyllでのURLとWordPressでのURLをチェックする簡単なPythonのコードスニペットを書いて、手動で修正した。
