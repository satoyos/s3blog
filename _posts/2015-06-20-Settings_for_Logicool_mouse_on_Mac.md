---
layout: post
title: ロジクールマウスをMacで使うためにしたことメモ
tags:
- Mac OS X
---

ロジクールマウスをMacBook Proで使えるようにした際の設定項目メモ。

# 1. やることの概要

1. Logicool Control Center （Mac用のドライバ&設定アプリ）のインストール
2. Scroll Reverserをインストールし、Magic TrackPadとLogicoolマウスでスクロールの方向を逆に設定する。

# 2. Logicool Control Centerのインストール

[Logicoolのサイト](http://support.logicool.co.jp/product/anywhere-mouse-mx)から、M905のドライバをダウンロードし、インストールした。 <br>
設定アプリ（Logicool Control Center）上で、マウスカーソルの速度やボタンの割り当てなど、いろいろと設定をカスタマイズできる。

[![Logicool Control Centerの設定画面][1]][1]

  [1]: /images/LogicoolControlCenter_SS.png


# 3. Scroll Reverserをインストール

### TrackPadとマウスの違い

これでマウスが思ったように使えるようになるんだけど、残念なことに、**TrackPadのスクロールとマウスのスクロールでは、自然に感じるスクロール方向が逆になる。**

TrackPadの時は、スクロールの方向を「ナチュラル」に設定しておけばiPhone/iPadと同じ感覚でスクロールできたんだけど、その設定はマウスのスクロールにも影響し、これがWindowsで使っていたマウスのスクロールと完全に逆になってしまう。

[![スクロールの方向の設定画面][2]][2]

  [2]: /images/ScrollSettingsFoMouse.png

### 解決策 - Scroll Reverser

そこで、Webで解決法を検索してみたところ、[Scroll Reverser](http://pilotmoon.com/scrollreverser/)が見つかった。<br>
いやー、これは助かる！

[![Scroll Reverserの設定][3]][3]

  [3]: /images/ScrollReverserSS.png


これで完璧！＼(^o^)／