---
layout: post
title: VirtualBox上のUbuntuにSDカードを認識させる
tags:
- Raspberry PI
category: RasPi
---

- 最終目的は、Raspberry PI2の上で[POPFile](http://getpopfile.org/docs/jp)を動かすこと。
- そのために、まずはRaspberry PI2でUbuntuを動かそう！と考えた。
    - どうも、POPFileをパッケージインストール一発でセッティングできるらしい、という話を聞いて…。)
    - その際の参考に使用と思っているのが、[こちらの記事](http://gihyo.jp/admin/serial/01/ubuntu-recipe/0362)。
- でも、Raspberry PIに差して使うSDカードにUbuntuを導入するには、Linux環境が必要らしく…。
- だったら、VirtualBoxでUbuntuの仮想マシンを起動させて、そこからSDカードにアクセスできるようにしましょう！←今ココ

# 環境
こんな感じ。
![このMacについて](/images/aboutMyMac.png)

- マシン: MacBook Pro Retina15 (Late 2013)
- OS: Yosemite; OS X 10.10.2
- VirtualBox: Ver.4.3.22

# USBカードリーダーとして設定してみる
何も考えずに、SDカードをMacのスロットに差し、そのカードリーダー(USB)をVirtualBoxのゲストOS用設定画面で割り当ててみる。(仮想マシンは電源オフ状態)
![SDカードリーダ割り当て-初回](/images/VBManager_USB01.png)

そのまま仮想マシンを起動してみる。=> **起動しません (T_T)**
![起動失敗](/images/USB2-Unsupported-Error.png)

どうやら、[Oracle VM VirtualBox Extention](https://www.virtualbox.org/wiki/Downloads)なるものをダウンロードして入れる必要があるらしい…。

なら、入れてみましょう！(｀・ω・´)
![Install Exttention](/images/Install_Extention.png)


入りました。( ˘ω˘)
![Extention is installed now](/images/Extension_Installed.png)

今度は仮想マシンの起動に成功した。(vagrantで起動)
![仮想マシン起動成功](/images/vagrant_up_succeeded.png)

でも、`lsblk`を実行してみたら、SDカードが認識されない…。orz
![USB経由では認識されず](/images/NotRecognized_via_USB.png)

# SDカードを認識させる方法について調べ直す

ググってみると、StackOverflowに[関連記事](http://superuser.com/questions/373463/how-to-access-an-sd-card-from-a-virtual-machine)を見つけた。それによると…

> On newer MacBook Pro's the SD card slot is no longer exposed as a USB device. Because of this you'll need to attach the raw device to the VM in order to gain raw access to the whole card. 

*「SDカードスロットはUSBデバイスとしてではなく、rawデバイスとして利用すべし」*、とのこと。

さらに検索を続けると、下記ブログ記事を見つけた。同じようなことに苦労したため、rawデバイスとしてVirtualBoxから利用する方法を丁寧に解説してくれている。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://www.geekytidbits.com/mount-sd-card-virtualbox-from-mac-osx/" title="Mount SD card in VirtualBox from Mac OS X Host | Geeky Tidbits" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://www.geekytidbits.com/mount-sd-card-virtualbox-from-mac-osx/" width="120" height="90" style="float: left;"></a><a href="http://www.geekytidbits.com/mount-sd-card-virtualbox-from-mac-osx/" title="Mount SD card in VirtualBox from Mac OS X Host | Geeky Tidbits" target="_blank">Mount SD card in VirtualBox from Mac OS X Host | Geeky Tidbits</a><a href="http://b.hatena.ne.jp/entry/http://www.geekytidbits.com/mount-sd-card-virtualbox-from-mac-osx/"><img src="http://b.hatena.ne.jp/entry/image/http://www.geekytidbits.com/mount-sd-card-virtualbox-from-mac-osx/" alt="はてなブックマーク - Mount SD card in VirtualBox from Mac OS X Host | Geeky Tidbits" title="はてなブックマーク - Mount SD card in VirtualBox from Mac OS X Host | Geeky Tidbits"></a><br style="clear: both;" /></div>

この記事のステップに従ってみることにする。

# SDカードをrawデバイスとして認識させる

Macのターミナルから`mount`コマンドを実行してみると、SDカードは`/dev/sd2s1`に割り当てられていることが判明した。このデバイス名を、後で使う。

Macのディスクユーティリティで**アンマウント**ボタンを押す。参考記事に書いてあるように、ここは間違って**取り出す**ボタンを押さないように注意。

![SDカードのアンマウント](/images/UnmountSD.png)

次に、「VirtualBox vmdkファイル」なるものを作る。これには、

{% highlight sh %}
$ sudo VBoxManage internalcommands createrawvmdk -filename ./sd-card.vmdk -rawdisk /dev/disk2
{% endhighlight %}

というコマンドを実行する。デバイス名は、元記事では`/dev/disk1`となっていたところだけど、自分の環境に合わせて`/dev/disk2`で。

![vmdkファイルの作成](/images/VBoxManage command.png)

vmdkファイルができた。

次は、確実にマウントできるようにするため、今作ったばかりのvmdkファイルと、SDカードに対応するデバイス`/dev/disk2`をchmodで777にする。
![デバイスとvmdkファイルをchmod](/images/chmod_vmdk_and_device.png)

元記事通り、順調に進んだ。最後に、VirtualBoxの[ストレージ]タブから、SDカードを追加する。「ハードドライブを追加」を押すと出てくるダイアログで、「既存のディスクを選択」を選ぶ。
![「既存のディスクを選択」を選ぶ](/images/ChooseExistingDisk.png)

『ストレージツリー」に追加できた！
![vmdk追加成功](/images/vmdkIsAddedNow.png)

この状態で、VirtualBoxを起動してみると… `lsblk`コマンドにて、8GBのSDカードを確認！
![lsblk on Ubuntu](/images/foundSDcard.png)

やれやれ、ようやくVirtualBox上のUbuntuで認識するところまでできた。

# まとめ

今どきのMacbook ProでVirtualBox上のLinuxにSDカードを認識させるには、USBデバイス(の中のカード)としてではなく、rawデバイスとして認識させることが大事だった。

次は、VirtualBox上のUbuntuからやっと認識できたSDカードに、Raspberry PI用のUbuntuをインストールしようと思う。