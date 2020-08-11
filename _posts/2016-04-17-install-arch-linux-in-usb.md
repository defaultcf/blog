---
title: "Arch LinuxをUSBにインストールする"
date: "2016-04-17"
layout: "post"
---

最近、自分の周りでArchLinuxユーザーが急増中していて、自分も驚いています。確かに自分は事あるごとに周りの人をArchLinuxArchLinuxと洗脳してきましたが、ここまで功を奏するとは。

そこで、今回はUSBメモリにArchLinuxをインストールする方法をご紹介します。そうすることで自分のArchLinuxを行く先々の端末でブート出来る他、既存のOSを破壊せずに済みます（）。

尚、以下の作業は自己責任にて行ってください。デバイス名を間違えただけでもHDDの中身をフォーマットしかねないので。

### 環境構築

ライブUSBを作るための環境をセットアップします。

- ライブUSBにするためのUSB(4GB以上)
- ここから取ってきたディスクイメージを使った仮想環境

なお、VirtualBoxのくだりでは、仮想環境用のストレージを用意する必要はありません。何故なら本当に必要ありませんし、間違ったインストール先を選択するというミスを減らすことが出来るからです。

以上が準備出来たら、USBを仮想環境側でも認識させて立ち上げます。以降からは仮想環境上での作業です。

### インストール前の準備

lsblkでUSBがどこにマウントされているかを確認し、その後パーティションを最適化します。...と、その前にpacmanでpartedとdosfstoolsをインストールします。

parted /dev/sdx

(parted) mklabel msdos

#既にパーティションがある場合

(parted) rm パーティション番号

(parted) mkpart primary ext4 1MiB 100%

(parted) set 1 boot on

(parted) quit

mkfs.ext4 -O "^has\_journal" /dev/sdxx

作成したパーティションを/mntにマウントします。

mount /dev/sdxx /mnt

/etc/pacman.d/mirrorlistをお好みのエディタで編集して、.jpが付いているURLを上の方に持ってきます。

### ベースシステムのインストール

終わったら早速ベースシステムをインストールしましょう。

pacstrap -i /mnt base base-devel

genfstab -U /mnt >> /mnt/etc/fstab

次のように/mnt/etc/fstabを編集してrelatimeをnoatimeに変えておきます。

\- UUID=\[USBのSSIDが長々と\]    /    ext4    rw,relatime    0 1

+ UUID=\[USBのSSIDが長々と\]    /    ext4    rw,noatime    0 1

### chrootでインストールしたシステムに入る

arch-chroot /mnt /bin/bash

### 言語設定

\- #en\_US.UTF-8 UTF-8

+ en\_US.UTF-8 UTF-8

...

- #ja\_JP.UTF-8 UTF-8

+ ja\_JP.UTF-8 UTF-8

locale-gen

echo LANG=en\_US.UTF-8 > /etc/locale.conf

export LANG=en\_US.UTF-8

KEYMAP=jp106
FONT=lat9w-16

### タイムゾーンの設定

ln -s /usr/share/zoneinfo/Asia/Tokyo /etc/localtime

### ホスト名の設定

echo ホスト名 > /etc/hostname

### ネットの設定

pacman -S iw wpa\_supplicant dialog

### rootのパスワード変更

passwd でrootのパスワードを変更しておくことを忘れずに。

### ブートローダーのインストール

pacman -S grub os-prober

grub-install --target=i386-pc --recheck --debug --force /dev/sdX

grub-mkconfig -o /boot/grub/grub.cfg

あとはexit してreboot します。

## さらなるセットアップ

useradd -m -G wheel ユーザー名

passwd ユーザー名

\- # Defaults env\_keep += "HOME"

+ Defaults env\_keep += "HOME"

...

- # %wheel ALL=(ALL) ALL

+ %wheel ALL=(ALL) ALL

su ユーザー名 で新しく作ったユーザーでログインします。今後は今作ったユーザー名で作業し、管理者権限が必要な時はsudoを使うようにします。

### デスクトップ環境構築

まずはビデオドライバを入れます。これは各自環境によって異なるので、lspci | grep -e VGA -e 3D で確認して必要なものをインストールします。自分はNvidiaのグラボを通しているので、xf86-video-nouveauをインストールしました。

pacman -S xorg-server xorg-server-utils xorg-xinit xf86-video-nouveau

pacman -S xmonad xmonad-contrib xterm

 

【2017年7月20日追記】

xorg-server-utilsがxorg-appsになった模様。

あと、Nvidia環境じゃなきゃxf86-video-nouveauではなくてxf86-video-vesaを入れよう。

 

#!/bin/sh
setxkbmap -layout jp
exec xmonad

早速startx したいところですが、ビデオドライバを新たにインストールしたので一旦reboot します。reboot したらstartx してxmonadに入ります。

xmonadにデフォルトで設定されている簡単なキーバインドは以下の通り。

Mod + Shift + Enter

ターミナルを開く

Mod + Shift + C

フォーカスしているウィンドウを閉じる

Mod + Shift + Q

ログアウトする

### 壁紙を設定する

xmonad起動時に真っ黒な画面が表示されるだけだと味気ないので、壁紙を設定しましょう。

pacman -S feh

feh --bg-scale イメージファイル

sh ~/.fehbg &

...

exec xmonad

これで壁紙が設定されます。

### AURをインストールできるようにする

pacmanでインストールできるパッケージには限度があります。それを補ってくれるyaourt（フランス語でヨーグルト）をインストールします。

\[archlinuxfr\]
SigLevel = Never
Server = http://repo.archlinux.fr/$arch

pacman --sync --refresh yaourt

### 日本語環境設定

pacman -S adobe-source-han-sans-jp-fonts

yaourt -S mozc ibus-mozc

export GTK\_IM\_MODULE="ibus"
export QT\_IM\_MODULE="ibus"
export XMODIFIERS=@im=ibus
ibus-daemon --xim -d &

...

exec xmonad

ibus-setup で設定画面を表示して、Generalタブでキーバインドを設定し、Show property panelをAlwaysにし、Input Methodタブでmozcを追加すればおk。

### サウンド

USB接続しているスピーカーから音を出したいので色々と弄る。

pacman -S alsa-utils

defaults.pcm.card 3
defaults.pcm.device 0
defaults.pcm.card 3

alsamixer でボリュームを上げてあげれば聞こえるはず。

 

これを読んでいるあなたはきっと「もっとこうしたい、ああしたい」と思い始めていることでしょう。そう、ArchLinuxなら自分の好きなデスクトップ環境に出来るのです。自分好みのデスクトップ環境探しに終わりはありません。まだまだ先は長いですが、頑張っていきましょう！

と、まぁこんな感じで2日目の記事はここまで。昨日と今日でそれぞれ3,000字程度書いてるけど、この調子で書けるかな...
