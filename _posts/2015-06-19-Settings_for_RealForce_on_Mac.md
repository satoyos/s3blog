---
layout: post
title: RealForceキーボードをMacで使うための覚書
tags:
- Mac OS X
---

RealFoeceキーボードをMacBook Proで使えるようにした際の設定項目メモ。

# 1. はじめに

Macのキーボードをカスタマイズするにあたって、ツールは二つ必要。

1. [Karabiner](https://pqrs.org/osx/karabiner/index.html.ja) (旧KeyKeyRemap4MacBook) : ほとんどのキー設定を行うアプリ
2. [Seil](https://pqrs.org/osx/karabiner/seil.html.ja) (旧PCKeyboardHack) : Karabinerでは設定できない「Caps Lockと左Ctrlの入れ替え」を設定する


# 2. Seilの設定

インストールした後、下図のような設定を行った。<br>
(スクリーンショットは旧名アプリのものだけど、やるべきことは同じ。)

[![SeilでCapsLockを設定][1]][1]

  [1]: /images/Seil_settings_for_RealForce.png

要は、**Change Caps Lockをチェックして、keycodeをダブルクリックし、書き換える。**

# 3. Karabinerの設定

2014/07/25のところの設定は下図の通り。(主要部分のみ)<br>
HHKBやParallels用の設定なども加わり、賑やかになった。

[![Karabinerでのキー設定][2]][2]

  [2]: /images/Karabiner_settings_for_RealForce.png

この中で、上から４つのチェックボックスは、設定ファイル`private.xml`に自分なりの設定を記述することで設定した。

### private.xmlファイルを開くには？

`private.xml`ファイルは、下図の要領で開く。<br>
(スクリーンショットは旧名アプリのものだけど、やるべきことは同じ。)

[![Karabinerでのprivate.xmlの開き方][3]][3]

  [3]: /images/HowToOpenPrivateXML.png

内容の書き方については、開発元のマニュアルページを読むと分かる。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="https://pqrs.org/osx/karabiner/xml.html.ja" title="private.xml Reference Manual - Karabiner - OS X用のソフトウェア" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?https://pqrs.org/osx/karabiner/xml.html.ja" width="120" height="90" style="float: left;"></a><a href="https://pqrs.org/osx/karabiner/xml.html.ja" title="private.xml Reference Manual - Karabiner - OS X用のソフトウェア" target="_blank">private.xml Reference Manual - Karabiner - OS X用のソフトウェア</a><a href="http://b.hatena.ne.jp/entry/https://pqrs.org/osx/karabiner/xml.html.ja"><img src="http://b.hatena.ne.jp/entry/image/https://pqrs.org/osx/karabiner/xml.html.ja" alt="はてなブックマーク - private.xml Reference Manual - Karabiner - OS X用のソフトウェア" title="はてなブックマーク - private.xml Reference Manual - Karabiner - OS X用のソフトウェア"></a><br style="clear: both;" /></div>

### private.xmlの設定例

現時点での`private.xml`の設定内容は、次の通り。

{% gist satoyos/8163b499fa050fe1c846 %}


# 4. まとめ

今のところ、本エントリーの設定でうまく動いている。

RealForceで文章を書いたりプログラミングしたりできるのは、思っていた以上の幸福感！