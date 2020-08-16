---
title: "MacBookProにArchLinuxを入れる"
date: "2016-04-20"
layout: "post"
---

今までMacBookProにArchLinux突っ込んで、OS XとArchLinuxと時々Ubuntuという環境で遊んできました。が、いつからかpacman -Syuでエラーが出るように…

いっそ再インストールしてやれ！という事で今回はその様子をお伝えします。

当方のMacBookProはEthernet接続が出来ないので、前もってWiFiを認識できるUbuntuをインストールして、そこからArchLinuxを引き込む形にします。

ブラウザから[http://ftp.jaist.ac.jp/pub/Linux/ArchLinux/iso](http://ftp.jaist.ac.jp/pub/Linux/ArchLinux/iso)にアクセスして、最新のブートストラップイメージ（bootstrap-20XX.XX.XX-x86\_64.tar.gzみたいなファイル）を取ってきて、それを/tmpに展開します。

cd /tmp

curl -O http://ftp.jaist.ac.jp/pub/Linux/ArchLinux/iso/2016.04.01/archlinux-bootstrap-2016.04.01-x86\_64.tar.gz

tar xzf archlinux-bootstrap-2016.04.01-x86\_64.tar.gz

/tmp/root.x86\_64/etc/pacman.d/mirrorlist を編集して、日本のリポジトリサーバーのURLのコメントを外しておきます。

sudo /tmp/root.x86\_64/bin/arch-chroot /tmp/root.x86\_64

pacman-key --init

pacman-key --populate archlinux

 

### ArchLinuxをインストールする

lsblk でArchLinuxをインストールするパーティションを確認し、ファイルシステムを作成します。

mkfs.ext4 -O "^has\_journal" /dev/sdxX

終わったら、/mntにマウントし、早速インストールします。

mount /dev/sdxX /mnt

pacstrap -i /mnt base base-devel

genfstab -U /mnt >> /mnt/etc/fstab

/mnt/etc/fstab を編集してreltimeをnoatimeに変更します。

arch-chroot /mnt /bin/bash でインストールしたシステムに入ります。

 

詳しいセットアップ方法については、今回は省略します。

 

### WiFiドライバのインストール

あと特筆すべき事があるとすれば、このタイミングでMacBookProに適したWiFiドライバを入れる事です。ここらでroot以外のユーザーでの操作が必要になるので、ユーザーを作成してそのユーザーでログインしておきます。

curl -O https://aur.archlinux.org/cgit/aur.git/snapshot/broadcom-wl.tar.gz

tar xzf broadcom-wl.tar.gz

cd broadcom-wl

makepkg -s

pacman -U broadcom-\*.pkg.tar.gz

pacman -S iw dialog wpa\_supplicant

ここまででエラーが出なければドライバのインストールに成功です。

 

### Retinaディスプレイに対応する

Section "Monitor"
    Identifier "<default monitor>"
    DisplaySize 394 245
EndSection
