---
layout: post
title: "SDカードにRaspberry PI用のUbuntuを導入してみる"
excerpt_separator: <!--more-->
tags:
- RaspberryPI
---

前に書いた記事[「VirtualBox上のUbuntuにSDカードを認識させる」](/2015/03/01/SD_for_Ubuntu/index.html)の続き。

今回は、VirtualBox上のUbuntuで認識できるようになたSDカードに、Raspberry PI用のUbuntuを載せてみる。お手本は、gihyo.jpの下記記事。

<!--more-->
<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://gihyo.jp/admin/serial/01/ubuntu-recipe/0362" title="第362回　Raspberry Pi 2でXubuntu 14.04を動かす：Ubuntu Weekly Recipe｜gihyo.jp … 技術評論社" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://gihyo.jp/admin/serial/01/ubuntu-recipe/0362" width="120" height="90" style="float: left;"></a><a href="http://gihyo.jp/admin/serial/01/ubuntu-recipe/0362" title="第362回　Raspberry Pi 2でXubuntu 14.04を動かす：Ubuntu Weekly Recipe｜gihyo.jp … 技術評論社" target="_blank">第362回　Raspberry Pi 2でXubuntu 14.04を動かす：Ubuntu Weekly Recipe｜gihyo.jp … 技術評論社</a><a href="http://b.hatena.ne.jp/entry/http://gihyo.jp/admin/serial/01/ubuntu-recipe/0362"><img src="http://b.hatena.ne.jp/entry/image/http://gihyo.jp/admin/serial/01/ubuntu-recipe/0362" alt="はてなブックマーク - 第362回　Raspberry Pi 2でXubuntu 14.04を動かす：Ubuntu Weekly Recipe｜gihyo.jp … 技術評論社" title="はてなブックマーク - 第362回　Raspberry Pi 2でXubuntu 14.04を動かす：Ubuntu Weekly Recipe｜gihyo.jp … 技術評論社"></a><br style="clear: both;" /></div>

# パーティション作成とマウント

参考記事の通りに、下記コマンドでパーティションを作成する。

{% highlight sh %}
$ export RASPI_SD=/dev/sdb
$ sudo parted -s ${RASPI_SD} mklabel msdos
$ sudo parted -s ${RASPI_SD} unit B mkpart primary fat32 4194304 71303167
$ sudo parted -s ${RASPI_SD} unit B mkpart primary ext4 71303168 100%
$ sudo parted -s ${RASPI_SD} set 1 boot on
$ sudo mkfs.fat  ${RASPI_SD}1
$ sudo mkfs.ext4 -L root ${RASPI_SD}2
{% endhighlight %}

これでパーティションは作成できたのだが…。参考記事には
>Ubuntuであれば，mkfsが終わると自動的にマウントされると思います。

と書いてあったものの、僕の環境では自動マウントされず。

仕方がないので、手動マウント。

{% highlight sh %}
$ sudo mount -t vfat /dev/sdb1 /media/ubuntu/boot
$ sudo mount -t ext4 /dev/sdb2 /media/ubuntu/root
{% endhighlight %}

これでマウントできた。
![SD_card_partition_mounted](/images/SDCard_Partition_mounted.png)

# ブートパーティションの作成
ここは参考記事通り、Snappyイメージをwgetで取得して、その先頭パーティションを`/mnt`にマウントし、SDカードのブートパーティションにコピーする。

{% highlight sh %}
$ wget -O pi-snappy.zip http://downloads.raspberrypi.org/ubuntu_latest
$ unzip pi-snappy.zip
$ sudo mount -o loop,offset=4194304,sizelimit=67108864 pi-snappy.img /mnt
$ sudo cp -r /mnt/* /media/ubuntu/boot
$ sudo umount /mnt
{% endhighlight %}

さらに参考記事通り、`/media/ubuntu/boot/cmdline.txt`を以下の内容に書き換える。(複数行に分かれて見えるかもしれないけど、1行で！)

{% highlight sh %}
dwc_otg.lpm_enable=0 console=ttyAMA0,115200 console=tty1 boot=local root=/dev/mmcblk0p2 rootfstype=ext4 rw panic=-1 fixrtc elevator=deadline rootwait
{% endhighlight %}

# ルートファイルシステムの作成

ひたすら、参考記事の通りに。

まず、ARM用のUbuntuを入手。

{% highlight sh %}
$ wget http://cdimage.ubuntu.com/ubuntu-core/releases/14.04.1/release/ubuntu-core-14.04.1-core-armhf.tar.gz
$ sudo tar xvf ubuntu-core-14.04.1-core-armhf.tar.gz -C /media/ubuntu/root/
{% endhighlight %}

そして、Raspberry PI2用のカーネルモジュールを瑠とファイルシステムに展開する。

{% highlight sh %}
$ sudo mount -o loop,offset=71303168,sizelimit=1073741824 pi-snappy.img /mnt
$ sudo cp -a /mnt/lib/modules /media/ubuntu/root/lib/
$ sudo umount /mnt
{% endhighlight %}

このファイルシステムは、最小構成で、一般ユーザーも存在しない状態。なので、

* 一般ユーザー「ubuntu」を作り、
* sudoグループに追加
* さらにネットワーク設定を行って、
* aptのuniverseとmultiverseを有効にしておく。

{% highlight sh %}
$ sudo apt install qemu-user-static
$ cd /media/ubuntu/root
$ sudo cp /usr/bin/qemu-arm-static usr/bin
$ sudo mount -t proc proc proc
$ sudo mount --rbind /dev dev
$ sudo mount --rbind /sys sys
$ sudo mount -o bind /etc/resolv.conf etc/resolv.conf
$ sudo chroot . /bin/bash

# adduser ubuntu
# addgroup ubuntu sudo
# echo "raspi2" > /etc/hostname
# echo "127.0.1.1       raspi2" > /etc/hosts
# echo "auto eth0" > /etc/network/interfaces.d/eth0
# echo "iface eth0 inet dhcp" >> /etc/network/interfaces.d/eth0
# sed -i "s/^# \(.*trusty.*verse.*\)/\1/g" /etc/apt/sources.list
# apt install openssh-server avahi-daemon
# exit

$ sudo umount etc/resolv.conf
$ sudo umount -l sys
$ sudo umount -l dev
$ sudo umount proc
$ sudo rm usr/bin/qemu-arm-static
{% endhighlight %}

# このSDカードをRaspberry PI2に挿してみる

動いた…。

![Ubuntu Raspberry PI2](/images/runningRPI.jpg)