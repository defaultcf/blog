---
title: "Macでcowsayならぬpokemonsayしてみた"
date: "2016-11-06"
layout: "post"
---

最近、シェル極めるぞ〜って意気込んでるIsaacです。tmux最高！

ところで、tmuxで時計と癒しが同時に欲しいと思うことってあるじゃないですか?? cowsayで時計表示させても良いんですけど、はっきり言って微妙。調べてみたらpokemonsayなるものがあったので、今回はMacでpokemonsayしてみました。

### インストール

まずはcowsayとfortuneを入れます。

brew install cowsay fortune

 

本命のpokemonsayをインストールします。

git clone https://github.com/possatti/pokemonsay

cd pokemonsay && ./install.sh

 

 

### トラブル1

これで入りました...って、勝手に${HOME}/bin作ってその中にpokemon{say,think}入れてんじゃねーか。**やめろ。**

ってことで、/usr/local/binにでも突っ込んでおきましょう。

cp ${HOME}/bin/pokemon\* /usr/local/bin/

これでpokemonsayならびにpokemonthinkコマンドが動けば、rm -r ${HOME}/bin しちゃいましょう。

 

### トラブル2

pokemonsayしたら「shufコマンドが無いぞ」って言われる...

入れましょう。

brew install coreutils

 

LinuxにあるのにMacに無かった、便利なコマンドたちが入ります。ただこのままだとgshufのように、どれも最初に'g'が付きます。今回使いたいのはshufコマンドなので、coreutilsインストール時に言われた通り.zshrc(.bashrc)に以下の行を追加します。

#追記
PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"
MANPATH="/usr/local/opt/coreutils/libexec/gnuman:$MANPATH"

 

これでおk。

pokemonsay Hello でランダムに登場するポケモンがHelloと言ってくれたら成功。

[![pokemonsay](/assets/img/94d3980291f71593e2bc276d9251ff02-1.png)](http://test.blog.i544c.me/wp-content/uploads/2016/11/94d3980291f71593e2bc276d9251ff02-1.png)

サンダースだ。可愛い。

### 時計を表示し続ける

本題に移りましょう。今回やりたいのは時間を教え続けてくれるポケモンの表示ですから、1秒ごとに時間をpokemonsayに渡すプログラムを作るだけです。

watchコマンドでやりたかったんですけど、ANSIコードが色に変換されて表示されない... なので、以下のようなプログラムを作成し、コマンド化しました。

#!/bin/sh
poke=\`pokemonsay -l | sed 1,2d | shuf -n 1\`
clear="\\033\[H\\033\[J"

while true; do
  poketime=\`date +%H:%M:%S | pokemonsay -n -p $poke\`
  echo $poketime | awk "BEGIN{print \\"$clear\\"} {print}" | cat
  sleep 1
done

pokemonsayは-pオプション無しでランダムにポケモンを表示しますが、それだと1秒ごとにポケモンが変わることになってしまいます。今回はpoketimeを実行するたびにポケモンを変えたいので、丁度入れたshufコマンドでランダムに選択することにします。

結果はこんな感じ。

[![poketime](/assets/img/poketime-1.gif)](http://test.blog.i544c.me/wp-content/uploads/2016/11/poketime-1.gif)

モンジャラだ。可愛い。

 

勿論、ランダムじゃなくて常にピカチュウなんてのも可能。pokemonsay -p Pikachu にするだけです。

tmuxだとこんな感じ。

[![poketime-on-tmux](/assets/img/poketime-on-tmux-1.jpeg)](http://test.blog.i544c.me/wp-content/uploads/2016/11/poketime-on-tmux-1.jpeg)

良い。
