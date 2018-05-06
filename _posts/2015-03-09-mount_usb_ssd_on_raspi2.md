---
layout: post
title: Raspberry PI2に外付けSSDをマウントする
tags:
- RaspberryPI
- Ubuntu
---

Ubuntuを導入したRaspberry PI2上でDockerを動かしてみるためには、外付けストレージの使用が推奨されている。裸のSSDが余っているので、こいつをUSB外付けケースに入れて、マウントしようと思う。

Raspberry PI2は、もしかしてUSB3.0をサポートしていないかな？と淡い期待を抱いたんだけど、やっぱりUSB2.0だった…。

仕方ないので、裸のSSDをUSB2.0の外付けケースに入れてRaspberry PIに繋ぐことにする。

# 電源はしっかり確保しよう

もしかして、Raspberry PIさえしっかりとした電源に繋いでおけば、*外付けSSDはRaspberry PIのUSBから給電する*、ということはできないかしらん？

とまたまた夢想してみるが、実際に試してみると、Ubuntuが外付けディスクを全く認識してくれない。

給電用USB端子付きのコードを使って、二本ともRaspberry PIのUSBソケットに挿してみるが、やっぱり認識されず。

仕方なく、給電用のUSB端子を*ちゃんと給電できる*USBソケットに繋ぎ替えてやると、ようやく外付けディスクを認識してくれた

![マウント成功](/images/usb_sdd_mount-successfu.jpg)

やっぱり、外付けディスクには真面目に給電してやらないとダメですね。

# 自動マウントの設定（fstab）

認識した外付けディスクの自動マウントには成功したので、`/etc/fstab`を編集して、再起動後も自動マウントできるように設定する。

まず、`blkid`コマンドで、デバイスのUUIDを調べておく。

![デバイスのUUIDを確認](/images/check_device_uuid.png)

そして、`/etc/fstab`にこのUUIDを使ってマウント用の情報を書く。以下は、ext4でフォーマット済みのデバイスを`/media/docker`フォルダにマウントする設定。

{% highlight sh %}
ubuntu@raspi2:~$ cat /etc/fstab
# UNCONFIGURED FSTAB FOR BASE SYSTEM
UUID=bfa8d404-e64b-4cb3-8a0a-4d3aeb291e26 /media/docker ext4 defaults 0 0
{% endhighlight %}

これで、再起動しても自動で外付けディスクをマウントできるようになった。