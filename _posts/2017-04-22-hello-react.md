---
title: "Hello React"
date: "2017-04-22"
layout: "post"
---

いい加減にJSXの食べず嫌いを治そうと思って、Reactを始めてみました。

まずは準備しましょう。折角なのでyarnを使います。Dockerのnode:7.9.0イメージなら最初から入っていました。

yarn init でnpmの時と同じようにpackage.jsonを作成します。

YarnでReactの最新のやつを落とします。

yarn add react react-dom

ついでにwebpackを使ってみることにします。これは開発の際に使うので、\--dev オプションで入れます。

yarn add --dev webpack

 

構成としてはこんな感じに作っていきます。

./app
├── dist
│   └── bundle.js
├── index.html
├── node\_modules
├── package.json
├── src
│   └── index.jsx
└── webpack.config.js

srcに小分けにしたjsxを書いていき、distにコンパイルしたbundle.jsを出力、index.htmlでそのファイルを読みに行く、といった感じです。

 

先にindex.htmlを作ってしまいましょう。

<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="utf-8">
    <title>Hello React</title>
</head>
<body>
    <div id="app"></div>
    <script src="dist/bundle.js"></script>
</body>
</html>

 

さぁここからが自分にとって未知の領域です。複数のサイトを見ながら慎重に進めます。

早速Reactの部分を書きます。ES2015を使って、importを楽にします。

import React from 'react';
import {render} from 'react-dom';

class App extends React.Component {
    render() {
        return (
            <p>Hello React!</p>
        );
    }
}

render(<App/>, document.getElementById('app'));

ほえぇ、って感じです。こんなもんだと自分に言い聞かせます。

 

これでソースは完成で、あとはwebpackでReactやらReact-DOMをまとめてdist/bundle.jsにするだけ！これが意外と面倒でした...

 

次を実行して、babel周りを入れます。

yarn add --dev babel-core babel-loader babel-preset-es2015 babel-preset-react

 

そしてwebpackの設定ファイルを書いて楽します。

var path = require('path');
const src  = path.resolve(\_\_dirname, 'src');
const dist = path.resolve(\_\_dirname, 'dist');

module.exports = {
    entry: src + '/index.jsx',
    output: {
        path: dist,
        filename: 'bundle.js'
    },
    module: {
        loaders: \[
            {
                test: /\\.jsx$/,
                exclude: /node\_modules/,
                loader: 'babel-loader'
            }
        \]
    }
};

 

最後にpackage.jsonに以下を追記します。

"babel": {
  "presets": \["react", "es2015"\]
}

 

これで./node\_modules/.node/webpackを実行すれば、distにまとまったbundle.jsが出力されるはずです。

これでやっとReactが出来る...！
