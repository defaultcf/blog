---
title: "ブログを移転しました"
date: "2017-05-22"
layout: "post"
---

と言ってもレンタルサーバからVPSに、です。

 

このブログの運用には今までレンタルサーバを使ってきましたが、如何せん重い。そこで[先日作成した](https://blog.i544c.me/2017/05/09/let-go-of-docker/)RedmineやGiteaを運用しているConoHaインスタンスにWP環境を移行することに。更にNginxとWPのプラグイン「WP Super Cache」を組み合わせて、それなりに高速化を図りました。

VPS上に設置したWPをセットアップ、旧環境からバックアップを作成して新環境に移行。DNSのAレコードを旧環境から新環境のIPに切り替えて1日くらい共存させておけば、ユーザーは違和感なく新環境のブログへと移行できるという算段でやりました。

 

ついでにテーマ周りを弄りました。直接テーマを弄ると、テーマアップデートが来た時に変更が失われるので、今回は子テーマを作りました。

<?php if( !current\_user\_can('administrator') ): ?>
<script>
//GoogleAnalyticsCode
</script>
<?php endif; ?>

わざわざGoogleAnalytics用のプラグインを入れずとも、この数行を書けばトラッキング出来ます。

<?php

$is\_jetpack = class\_exists('jetpack') && Jetpack::is\_module\_active('stats');

function admin\_posts\_columns($columns) {
  $view\_column = \['postviews' => '閲覧数'\];
  $columns = array\_merge(array\_slice($columns, 0, 2), $view\_column, array\_slice($columns, 2));
  return $columns;
}

function add\_posts\_custom\_column($column\_name, $post\_id) {
  if($column\_name == 'postviews') {
    echo stats\_get\_csv('postviews', array('days' => -1, 'limit' => 1, 'post\_id' => $post\_id))\[0\]\['views'\];
  }
}

if($is\_jetpack) {
  add\_filter('manage\_posts\_columns', 'admin\_posts\_columns');
  add\_filter('manage\_posts\_custom\_column', 'add\_posts\_custom\_column', 10, 2);
}

?>

管理画面の記事一覧ページに閲覧数を表示するようにしています。新たに閲覧数をカウントするプラグインを入れようかと思っていましたが、Jetpackから閲覧数を取ってこれるので解決しました。

さらに、

<?php echo stats\_get\_csv('postviews', array('days' => -1, 'limit' => 1, 'post\_id' => $post->ID))\[0\]\['views'\]; ?>

のようなのを書くことで、各記事のページでその閲覧数を表示させます。

 

最近は[Grav](https://getgrav.org/)という静的CMSが盛んで、自分も今試している最中ですが、WordPressの機能の豊富さはまだまだ魅力的なので、blog.i544c.meではもうしばらくWordPressを使っていきます！
