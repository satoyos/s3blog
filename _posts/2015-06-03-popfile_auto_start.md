---
layout: post
title: UbuntuでPOPFileを自動起動するための設定メモ
excerpt_separator: <!--more-->
tags:
- Ubuntu
- POPFile
- Raspberry PI
---

Raspberry PI2上のUbuntuでは、`apt-get`でPOPFileをインストールすることができた。

Mac OS X上のPOPFileからコピーしてきた`popfile.cfg`と`popfile.db`を`/var/lib/popfile/`フォルダに上書きすることで、これまで使ってきたMac上のPOPFileのデータを引き継ぐこともできた。

ただ、自動起動まではできないみたいなので、二点ほど変更を加えた。
<!--more-->

# 1. スタートスクリプト`start_popfile.sh`の書き換え

自分の環境に会わせて、`start_popfile.sh`の一部を書き換えた。

{% highlight sh %}
5,6c5,6
< UIPORT=7070
< UILOCAL=yes
---
> UIPORT=8080
> UILOCAL=no
{% endhighlight %}

一言で言うと、「LAN上の他のPCから、ポート8080でPOPfileの管理画面にアクセスできるようにする」という設定になる。

# 2. 開始スクリプトを起動時に読み込むための設定

`/etc/rc.local`ファイルに下記の一行を追加した。

{% highlight sh %}
sh /usr/share/popfile/start_popfile.sh
{% endhighlight %}

これで、`sudo shutdown -r now`で再起動をかけた結果、POPFileが自動起動して、他のPCからポート8080で管理画面にアクセスできることを確認した。

これでしばらく様子を見よう。
