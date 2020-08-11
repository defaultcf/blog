---
title: "ターミナルで動くビジュアライザ「cli-visualizer」は良いぞ"
date: "2016-12-14"
layout: "post"
---

ホモ下宿メンにcli-visualizer使ってるとこ見られて「かっけー」って言われたので、ご紹介します。

## インストール

### Arch Linux

pacman -S ncurses fftw

yaourt -S cli-visualizer

pacman -S pulseaudio pulseaudio-alsa

audio.source=pulse

再起動後、visコマンドで動きます。

 

### Mac

**※Macの場合、mpdなどのサービスを介さなきゃならないのでめんどい**

当然まかーならbrew入れてますよね？

え？入れてない？？ → [コチラ](http://brew.sh/index_ja.html)からどうぞ

brew install fftw

brew tap homebrew/dupes

brew install ncurses

git clone git@github.com:dpayne/cli-visualizer && cd cli-visualizer

./install.sh
