---
title: "jupyterのノートをTeXにエクスポートする時に気をつけたこと"
date: "2018-05-21"
layout: "post"
---

jupyterのI/OをTeXに移すのに，TeXエクスポートは大変ありがたい機能だが．．．自分の環境では上手くPDFに変換できなかった．解決策を以下に記す．

 

## コードの修正

jupyterが吐き出すコードでは正しくPDFにコンパイルできなかったので，以下のように修正した．

\- \\usepackage{graphicx}
+ \\usepackage{color}
+ \\usepackage\[dvipdfmx\]{graphicx}

- \\usepackage{hyperref}
+ \\usepackage\[dvipdfmx\]{hyperref}

+ \\author{Leo}

 

## TeXLive環境の対応

### 1\. TeXLiveを最新にする

最新でないとtlmgr installが使えないので．

wget ftp://ftp.u-aizu.ac.jp/pub/tex/CTAN/systems/texlive/tlnet/update-tlmgr-latest.sh
chmod +x ./update-tlmgr-latest.sh
./update-tlmgr-latest.sh -- --update

 

### 2\. ulemを使えるようにする

ulemのインストールにはxzdecが必要らしいので入れる．

apk add --no-cache xz
tlmgr install ulem

 

とりあえずこれで画像などもPDFで表示できるようになった．
