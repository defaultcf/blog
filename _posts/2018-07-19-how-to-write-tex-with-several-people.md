---
title: "TeXの共同執筆方法"
date: "2018-07-19"
layout: "post"
---

本学プロジェクト履修生に捧ぐ．

 

本学プロジェクト履修生は，現在中間報告書執筆の真っ最中であると思う．この報告書，ほとんどのプロジェクトではTeXで書くはずだ．TeX自体初めて書く人が殆どだろうし，ましてそれを複数名で書くというのは，なかなかの挑戦であると思う．

 

そこで，昨年度私の所属したプロジェクトでのTeXの共同執筆方法をここに記す．これを参考に，各々の共同執筆環境を作り上げてもらいたい．

 

なお，各々のTeXの環境構築についてはDockerで良いかなと思っている．素晴らしいイメージがDockerHubには上がっているので，それを使えば誰もがTeXからPDFへの変換は出来るはず．

[https://hub.docker.com/r/paperist/alpine-texlive-ja/](https://hub.docker.com/r/paperist/alpine-texlive-ja/)

 

で，共同執筆については，とりあえずこれを読んでもらいたい．

https://speakerdeck.com/i544c/how-to-write-tex-with-several-people

これはすぃふとプロジェクト2018年度用に書いたもの．

 

## 1\. 報告書管理

GitHubを使って報告書を管理するのが一番だと思う．

担当箇所でファイルを分け，メインのファイルでsubfiles を使って読み込めば，少なくともコンフリクトは免れる．

参考: [https://qiita.com/sankichi92/items/1e113fcf6cc045eb64f7](https://qiita.com/sankichi92/items/1e113fcf6cc045eb64f7)

 

GitHubでブランチの保護機能を使って，masterへのforce pushをできないようにしておく．あとレビュー無しにmasterにマージできないようにしておくこと．

自身の担当箇所を粗方書いたら，タイトルの先頭に\[WIP\]と付けてプルリクエストを出す．レビュアーに適当なメンバー/先生/TAを追加して，その人にレビューしてもらう．全員からapproveを貰って初めてmasterにマージする．こうすることで，十分に添削をしたものがmasterに入るわけ．

 

## 2\. PDF作成

先のGitHubを使った報告書管理に加えて，CIを使ってより良い共同執筆環境を整える．

CIサービスは幾つかあるが，特に**CircleCI**は無料でプライベートレポジトリのCIを行ってくれるので，これを薦める．

[https://circleci.com/](https://circleci.com/)

CircleCIにレポジトリを登録し，設定ファイルを書く．

<script src="https://gist-it.appspot.com/github/funswift/medical-final-report/blob/master/.circleci/config.yml"><span data-mce-type="bookmark" style="display: inline-block; width: 0px; overflow: hidden; line-height: 0;" class="mce_SELRES_start">﻿</span></script>

 

CircleCI 2.0からworkflowという機能が使えるようになったので，この設定ファイルのように『masterへのプルリクエストの時だけPDF変換を走らせる』ということができる．

これとかキャッシュをフル活用して必要の無いタスク実行時間を減らし，CircleCIの月の実行時間制限(1,500min/month)を超えないように努力しよう(まぁそうそう超えない)．

また，CircleCIで作成したPDFは，store\_artifacts を使ってCircleCIの管理するAmazon S3に保存することができるので，その保存先のURLをSlackに飛ばす，なんてこともできる．先生方やTAが添削しやすいね．

最後にGitHubのブランチの保護機能でCIのチェックが通らなければマージできないようにしておく．最終的な設定はこんな感じ．[![](/assets/img/somerepo_settings_branches_master.png)](https://blog.i544c.me/wp-content/uploads/2018/07/somerepo_settings_branches_master.png)

 

## 3\. 自動校正

環境構築の時間があれば，TextLintを使った自動校正までやろう．これは日本語のチェックとか，表記揺れの検出/置換でとても役に立つ．

ただTextLintのTeXのプラグインがまだ無い．AST変換...いつか作りたい...

去年はtextプラグインで強制的に読ませてた...ここにやり方書くのはアレなので，知りたい人はお問い合わせください．

 

 

そんなわけで，頑張ってほしい．
