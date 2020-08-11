---
title: "真面目にArchLinuxをUSBブートする"
date: "2017-11-01"
layout: "post"
---

今まではお遊び、これからは本気。

最近はVirtualBox内のArchLinuxで色々やっているのだが、中でブラウザを使うようになると流石にCPUやらメモリやらが足りなくなる。やっぱり仮想環境ではなくそのままブートしたいので、**また**USBブートすることにした。

**"また"**というのは、1年前に環境構築にハマっていてブログにも書き残しているのだ。が、情報が古いので今回書き直した。

https://blog.i544c.me/2016/04/18/install-arch-linux-in-usb/

https://blog.i544c.me/2016/04/20/install-archlinux-on-macbookpro/

インストール先のUSBの他に、[ArchLinuxのイメージ](http://ftp.jaist.ac.jp/pub/Linux/ArchLinux/iso/2017.10.01/archlinux-2017.10.01-x86_64.iso)を直に焼いたUSBないしDVDを用意する。ArchLinuxイメージでブートして、インストール先のUSBを/dev/sdbで認識するところまで準備した。

## ネットワーク接続する

これが手間取った。1年前にやった時はArchLinuxイメージでブートした時点でネットに繋がったものだが、最近のイメージでやると繋がらなかった。

どうもIPv6を優先的に繋ごうとするようだ。もうそんな時代なのか。。。まだIPv6の通っていない環境にいる俺はIPv4を使うべく、dhcpcdの設定を弄ることで解決した。

...
-- # clientid
++ clientid
...
-- buid
++ # buid
...

systemctl restart dhcpcd

## パーティション分け

今まで使っていたVM上でUSBにインストールしていく形にする。VirtualBoxでUSBをマウントして、まずはパーティションを分ける。

fdisk /dev/sdb

# ディスクラベルをgptにセット
コマンド：g

# EFIシステムパーティションを作成
コマンド：n
# パーティション番号と最初のセクタは未指定、尚512MBというのは推奨されるものであって、最小値は256MBである
最終セクタ：+512M
# パーティションタイプをEFI Systemに変更
コマンド：t
パーティションタイプ：1

# ルートパーティションを作成
コマンド：n
# あとは全部未指定でおｋ

# 変更をディスクに書き込んで終了
コマンド：w

EFIシステムパーティションをfat32で、ルートパーティションをext4でフォーマットする。

pacman -S dosfstools
mkfs.fat -F32 /dev/sdb1
# ジャーナリングを無効化して読み書きを減らしているが、電源喪失時にファイルシステムが壊れる恐れあり
mkfs.ext4 -O "^has\_journal" /dev/sdb2

## マウントして、インストール

mount /dev/sdb2 /mnt
mkdir /mnt/boot
mount /dev/sdb1 /mnt/boot
pacstrap /mnt base base-devel

## マウントのルールを設定

genfstab -U /mnt >> /mnt/etc/fstab

arch-chroot /mnt /bin/bash

## 設定諸々

ロケールを設定。

en\_US.UTF-8 UTF-8
ja\_JP.UTF-8 UTF-8

locale-gen

LANG=en\_US.UTF-8

ネットワークを設定。

pacman -S iw wpa\_supplicant dialog

rootのパスワードを設定。

passwd

## ブートローダーをセットアップ

pacman -S grub
grub-install --target=x86\_64-efi --efi-directory=/boot --bootloader-id=grub --removable --recheck
grub-mkconfig -o /boot/grub/grub.cfg

これでとりあえずUSBは出来上がる。あとは自分好みに育てていくだけ👍

* * *

以下にオススメの設定や入れるべきソフトを記す。

### Grubの選択時間を短縮する

GRUB\_TIMEOUT=2

grub-mkconfig -o /boot/grub/grub.cfg で設定を適用する。

### WatchDogを無効化する

/etc/default/grubでGRUB\_CMDLINE\_LINUX\_DEFAULTにnowatchdogを追記．/etc/modprobe.d/nowatchdog.confにblacklist iTCO\_wdtを入力．

### タイムゾーンを設定

ln -sf /usr/share/Zoneinfo/Asia/Tokyo /etc/localtime
timedatectl set-local-rtc true #Windowsの時間に合わせる

### Wi-Fiに繋ぐ

pacman -S networkmanager gnome-keyring network-manager-applet xfce4-notifyd

nmcli dev wifi connect <name> password <password>

### デスクトップ環境を整える

pacman -S xorg-server xorg-apps xf86-video-nouveau

pacman -S xfce4 xfce4-goodies xcompmgr xmonad xmonad-contrib lightdm lightdm-gtk-greeter
systemctl enable lightdm

useradd -mG wheel $USER
passwd $USER

visudo #wheelグループのユーザーがsudoを使えるようにする

これで再起動したらログイン画面に遷移して、ログインしたらxfceが起動する。

import XMonad
import XMonad.Config.Xfce

main xmonad xfceConfig
  { terminal = "xfce4-terminal"
  , modMask  = mod4Mask
  }

よりカスタマイズしたいなら、ココを見ると良い。

[http://dotshare.it/category/wms/xmonad/](http://dotshare.it/category/wms/xmonad/)

xfceの中でxmonadを使えるようにしたいなら、設定→セッションと起動→自動開始アプリケーションでxmonad --replaceとxcompmgr -cCを追加する。

### xmobar

pacman -S xmobar stalonetray

### もっと見た目を整える

pacman -S arc-gtk-theme
yaourt -S arc-icon-theme

### Bluetoothマウスを使いたい

[ArchLinux Wiki](https://wiki.archlinux.jp/index.php/Bluetooth_%E3%83%9E%E3%82%A6%E3%82%B9)に全て載っている。

pacman -S bluez bluez-utils
systemctl enable bluetooth

bluetoothctl
\[bluetooth\]# list
#接続されているbluetoothコントローラーが表示される
\[bluetooth\]# select <コントローラーのMACアドレス>
\[bluetooth\]# power on
\[bluetooth\]# scan on
\[bluetooth\]# agent on
\[bluetooth\]# devices
#周囲のBluetoothデバイスが表示される
\[bluetooth\]# pair <マウスのMACアドレス>
\[bluetooth\]# trust <マウスのMACアドレス>
\[bluetooth\]# connect <マウスのMACアドレス>

exit

起動時に自動で接続したいなら、以下のようにAutoEnableをtrueにするだけ。

\[Policy\]
AutoEnable=true

### 日本語入力したい

pacman -S fcitx-im fcitx-configtool fcitx-mozc

export GTK\_IM\_MODULE=fcitx
export QT\_IM\_MODULE=fcitx
export XMODIFIERS=@im=fcitx

### ターミナルで絵文字が使いたい

[noto-fonts-emoji](https://www.archlinux.jp/packages/?name=noto-fonts-emoji)を入れておけば良い。

### Profile-sync-daemon

X Window SystemないしWaylandを入れたら、まぁまずはブラウザを入れてしまう俺。このブラウザはディスクの読み書きを恐ろしいほど行うので、この読み書きをUSBではなくRAMで行うようにする。[AUR](https://wiki.archlinux.jp/index.php/Yaourt)で[profile-sync-daemon](https://aur.archlinux.org/packages/profile-sync-daemon/)を入れて、$HOME/.config/psd/psd.confを編集して有効化するブラウザを選択して、systemctl --userで有効化するだけ。

ただこれだけだと$HOME/.cacheにどんどんキャッシュされるので、結局嬉しくない。[ここ](_wp_link_placeholder)を参考に、$HOME.cacheをメモリとしてマウントする。

### ターミナルでファイルマネージャー

pacman -S ranger w3m

xfce-terminalはw3m-imgが上手く動かない。ターミナルはrxvt-unicodeを使うようにしよう。

### USB接続のスピーカーを使えるようにする

pacman -S pulseaudio pavucontrol xbindkeys
yay -S pulseaudio-ctl
xbindkeys -d > ~/.xbindkeysrc
pactl list sinks short #サウンドカードの番号を確認

...
# set-sink-muteやset-sink-volumeの後の数字はサウンドカードの番号
"pactl set-sink-mute 1 false ; pactl set-sink-volume 1 +5%"
  XF86AudioRaiseVolume

"pactl set-sink-mute 1 false ; pactl set-sink-volume 1 -5%"
  XF86AudioLowerVolume

xbindkeys &

もしくはAURのxfce4-volumed-pulseを使う。

### dアニメなどでDRMコンテンツを観る

yaourt -S chromium pepper-flash chromium-widevine hal-flash(要らないかも)
systemctl enable hal-flash

### NTFSフォーマットのドライブをマウントする

pacman -S ntfs-3g
# マウントするドライブのUUID確認
ls -l /dev/disk/by-uuid

\# 追記

UUID=xxxxxxxxxxxxxxxx   /media/hdd   ntfs-3g   default,gid=1000,uid=1000,umask=0022   0   0

### Git Diff

ln -s /usr/share/git/diff-highlight/diff-highlight /usr/local/bin/diff-highlight

\[pager\]
  log = diff-highlight | less
  show = diff-highlight | less
  diff = diff-highlight | less

### NTPを使う

デフォだと時刻同期をしないので、どんどん時間が正しくなくなる。

timedatectl set-ntp true

### Pacman

いくつか設定しなければならない．

Pacmanはアップデートしても過去のバージョンを保持し続ける．pacman-contribをインストールして，過去のバージョンをHookで消す．

\[Trigger\] Operation = Remove Operation = Install Operation = Upgrade Type = Package Target = \*

\[Action\] Description = Keep the last cache and the currently installed. When = PostTransaction Exec = /usr/bin/paccache -rvk2

あと，XMonadはhaskellが更新されたらxmonad --recompileを実行しなきゃならないので，自動化する．

/etc/pacman.d/hooks/xmonad.hook

\[Trigger\] Operation = Install Operation = Upgrade Type = Package Target = xmonad\* Target = haskell\*

\[Action\] When = PostTransaction Exec = /usr/bin/xmonad --recompile

## 諸注意

ArchLinuxを運用する上で、気を付けるべきことをここに記す。

1. 1週間に1回はpacman -Syu
2. 動かなくなったら、とにかくログを遡れ

そんなこんなで、最近のデスクトップ事情はこんな感じ。

[![](/assets/img/e5db97d6a0bfcdf5150137c143f74073.png)](https://blog.i544c.me/wp-content/uploads/2017/11/e5db97d6a0bfcdf5150137c143f74073.png)

* * *

# 近況報告

デレステで二宮飛鳥役の志貴様をとうとうサブスクしたんだが、その時のご様子。

<iframe width="640" height="360" src="https://clips.twitch.tv/embed?clip=StormyBovineKaleVoHiYo&amp;autoplay=false&amp;tt_medium=clips_embed" scrolling="no" allowfullscreen="allowfullscreen" frameborder="0"></iframe>

可愛すぎかよ、一生サブスクするわ。
