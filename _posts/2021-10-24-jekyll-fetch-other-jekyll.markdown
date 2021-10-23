---
layout: post
title: Jekyll で 他の Jekyll から情報を取得する
categories: tech jekyll
---

需要が無いかもしれない技術メモ。

Jekyll で作っている2つのサイトがある。
- [default.cf](https://default.cf/) ←ポートフォリオサイト
- [default.cf/blog](https://default.cf/blog/) ←ブログ

同じドメインでホストしているが、これらは別々のリポジトリで GitHub Pages を使ってビルドされている。そのため、ドメインを跨いで情報を取得することは基本的にはできない。

今回は、ポートフォリオサイト上にブログのリンクを表示したい。
色々考えた末、jekyll-feed で生成される feed.xml を、何らかの方法でポートフォリオの `_data/feed.json` に置くことができれば良いとなった。

技術的には次の挑戦が必要だった。
- XML ファイルを JSON ファイルに変換する
- ポートフォリオサイトの GitHub Actions で XML ファイルの取得、JSON ファイルへの変換、コミット
- ブログを書いて master にプッシュしたら、ポートフォリオサイトの action が発火され更新される

順に説明する。


## XML ファイルを JSON ファイルに変換する

xml2json を使って変換することにした。
詳しくは Scrapbox に書いた。

- [scrapbox.io/defaultcf/xml2json](https://scrapbox.io/defaultcf/xml2json)
- [scrapbox.io/defaultcf/XML_Schema](https://scrapbox.io/defaultcf/XML_Schema)

XML Schema を頑張って書いて、変換できることを確認した。
ただ一箇所、XML 側を修正しないとどうしても変換できないところがあって、そこは処理前に sed で置換することにした。
```bash
sed -e 's/xmlns=/xmlns:atom=/'
```


## ポートフォリオサイトの GitHub Actions で XML ファイルの取得、JSON ファイルへの変換、コミット
試行錯誤して最終的にこうなった。

`.github/workflows/create-blog-links.yml`
```yaml
name: Create blog links
on: workflow_dispatch
jobs:
  fetch-and-create:
    runs-on: ubuntu-latest
    steps:
      - run: sudo apt update && sudo apt install -y libxml-compile-perl libjson-xs-perl
      - uses: actions/checkout@v2
      - run: |
          curl https://default.cf/blog/feed.xml |\
          sed -e 's/xmlns=/xmlns:atom=/' |\
          xml2json -x - -s feed.xsd > _data/feed.json
      - uses: EndBug/add-and-commit@v7
        with:
          message: 'Update feed.json'
          committer_name: GitHub Actions
          committer_email: 41898282+github-actions[bot]@users.noreply.github.com
```

これだけで済むのだから、GitHub Marketplace 様様だ。

手動で workflow を実行して、feed 取得・JSON に変換・コミットが為されることを確認する。


## ブログを書いて master にプッシュしたら、ポートフォリオサイトの action が発火され更新される
GitHub REST API を叩いて、他の workflow を発火することができるらしい。
ブログ側の action をこのように書いた。これでいけるはず...

`.github/workflows/kick-portfolio.yml`
```yaml
name: Kick portfolio
on:
  push:
    branches:
      - master
jobs:
  post-api:
    runs-on: ubuntu-latest
    steps:
      - uses: octokit/request-action@v2.x
        with:
          route: POST /repos/{owner}/{repo}/actions/workflows/{workflow_id}/dispatches
          owner: defaultcf
          repo: defaultcf.github.io
          workflow_id: create-blog-link.yml
        env:
          GITHUB_TOKEN: {% raw %}${{ secrets.GITHUB_TOKEN }}{% endraw %}
```

---

以上。さて、この記事を push したら、workflow がまわるかな...？
結果が出たら、この記事を更新する。
