---
title: "Laravelインストールしても動かない問題とその対処法"
date: "2016-06-16"
layout: "post"
---

久しぶりに新たにLaravelで何か作ろうかなと、laravel new project したらゾロゾロとエラーが...。あれーおっかしーなーと思いつつcomposer create-project laravel/laravel project --prefer-dist したらエラーは出ないが、実際にブラウザで表示させようとしたらエラーを吐く...

ググっても出てこないので、自分で色々と試して解決法を見つけたのでココに掲載する。

といってもそんな面倒でもなくて、以下のコマンド打つだけ。

composer update

php artisan key:generate

composer.jsonに基づいてファイル類の更新と、何故か生成されなくなったkeyを生成する。これだけ。あと他の作業はいつも通り。

* * *

いやーこれはゆゆ式由々しき問題ですぞ。だって[公式のリファレンス](https://laravel.com/docs/5.2)に書いてないじゃん。これじゃあLaravel始めようとしてる人たちが設置の時点で頓挫しちまう...
