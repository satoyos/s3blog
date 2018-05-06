---
layout: post
title: Apple WatchのRemoteアプリでiTunesに接続できないときの対処メモ
tags:
- Windows
- iTunes
- AppleWatch
---

Apple Watchで、PC（Windows 7）上のiTunesの音楽再生をコントロールしたいのに、PCに接続することができなかった。

対処法を調べてみたら、「PC上のARPテーブルを更新すると良い」ということが分かったので、**ARPキャッシュをいったんクリア**してみたら、ちゃんと接続することができた。

以下は、その手順についてのメモ。

# 症状

Apple WatchのRemoteアプリからiTunesに接続しようとすると、Apple Watch上に
iTunesに次の画面が出るとともに、PCのiTunesにはWatchデバイスのアイコンが現れる。

[![Apple WatchのRemoteがパスコードを表示][1a]][1a]

  [1a]: /images/Remote_on_Watch_requires_passcode.png

iTunes上のアイコンをクリックすると、「Apple Watchに表示されているパスコード」の入力を促される。

[![iTunesがパスコードの入力を要求][1b]][1b]

  [1b]: /images/passcode_required_on_iTunes.png

しかし、なぜか「パスコードが間違っています」と蹴られる。間違っていないはずなのに…。

[![iTunesがパスコードの入力を要求][2]][2]

  [2]: /images/passcode_rejected_on_iTunes.png

何度入力しても、結果は同じ。<br>
「Apple Watch パスコード」でググってみても、芳しい結果は得られず。


# そもそもiPhoneのRemoteがiTunesに接続できない

しばらく使ってなかったけれど、iPhoneのRemoteではちゃんとiTunesに繋がるだろうか？
と確認してみたら、案の定iPhoneでもRemoteでPC上のiTunesが見えていないことが判明。

ここでようやく、Apple Watch固有の問題ではなく、より一般的な「iPhoneのRemoteアプリがPCのiTunesに接続できない」という問題であることに気付いた。

そういうスタンスでググってみると、色々なノウハウが見つかった。

# ARPテーブルの更新で解決した事例あり

真っ先にヒットしたのが、下記エントリー。どうも、PCが保持するARPテーブル上でiPhoneのIPアドレスエントリーが無効になっていると、この問題が発生するらしい。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://re-boost.cocolog-nifty.com/blog/2010/11/ipadremote-a5fa.html" title="iPadでRemoteアプリが使えなくて困っていた件について: エレクトロNOW!" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://re-boost.cocolog-nifty.com/blog/2010/11/ipadremote-a5fa.html" width="120" height="90" style="float: left;"></a><a href="http://re-boost.cocolog-nifty.com/blog/2010/11/ipadremote-a5fa.html" title="iPadでRemoteアプリが使えなくて困っていた件について: エレクトロNOW!" target="_blank">iPadでRemoteアプリが使えなくて困っていた件について: エレクトロNOW!</a><a href="http://b.hatena.ne.jp/entry/http://re-boost.cocolog-nifty.com/blog/2010/11/ipadremote-a5fa.html"><img src="http://b.hatena.ne.jp/entry/image/http://re-boost.cocolog-nifty.com/blog/2010/11/ipadremote-a5fa.html" alt="はてなブックマーク - iPadでRemoteアプリが使えなくて困っていた件について: エレクトロNOW!" title="はてなブックマーク - iPadでRemoteアプリが使えなくて困っていた件について: エレクトロNOW!"></a><br style="clear: both;" /></div>

>そうしたところ、なんと「パスコードを検証中」のところでWindows7マシンがARPリクエストを出しているのにiPad側が反応していないことが判明した。arpコマンドで「arp -a -v」(-vオプションを付けるのがポイント)と入力してWindows7マシンのARPテーブルを確認したところ、案の定iPadのIPアドレスのエントリが無効になっている。

この*ARPテーブル*って何？ということについては、下記エントリーが図入りで分かりやすく解説してくれている。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://blog.putise.com/mac%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%A8arp%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%AE%E9%81%95%E3%81%84%E3%80%82l2%E3%82%B9%E3%82%A4%E3%83%83%E3%83%81/" title="MACアドレステーブルとARPテーブルの違い。L2スイッチにARPテーブルは必要か？ | puti se blog" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://blog.putise.com/mac%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%A8arp%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%AE%E9%81%95%E3%81%84%E3%80%82l2%E3%82%B9%E3%82%A4%E3%83%83%E3%83%81/" width="120" height="90" style="float: left;"></a><a href="http://blog.putise.com/mac%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%A8arp%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%AE%E9%81%95%E3%81%84%E3%80%82l2%E3%82%B9%E3%82%A4%E3%83%83%E3%83%81/" title="MACアドレステーブルとARPテーブルの違い。L2スイッチにARPテーブルは必要か？ | puti se blog" target="_blank">MACアドレステーブルとARPテーブルの違い。L2スイッチにARPテーブルは必要か？ | puti se blog</a><a href="http://b.hatena.ne.jp/entry/http://blog.putise.com/mac%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%A8arp%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%AE%E9%81%95%E3%81%84%E3%80%82l2%E3%82%B9%E3%82%A4%E3%83%83%E3%83%81/"><img src="http://b.hatena.ne.jp/entry/image/http://blog.putise.com/mac%E3%82%A2%E3%83%89%E3%83%AC%E3%82%B9%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%A8arp%E3%83%86%E3%83%BC%E3%83%96%E3%83%AB%E3%81%AE%E9%81%95%E3%81%84%E3%80%82l2%E3%82%B9%E3%82%A4%E3%83%83%E3%83%81/" alt="はてなブックマーク - MACアドレステーブルとARPテーブルの違い。L2スイッチにARPテーブルは必要か？ | puti se blog" title="はてなブックマーク - MACアドレステーブルとARPテーブルの違い。L2スイッチにARPテーブルは必要か？ | puti se blog"></a><br style="clear: both;" /></div>

>ARPテーブルとは、通信したいサーバー(IPアドレス)に対しそのパケットにMACアドレスを埋め込むために使う表です。<br>
**MACアドレスとIPアドレスの対応表というわけです。**

# ARPキャッシュの全クリア

iPhoneのエントリーを書き換えるのではなく、いっそのことのARPキャッシュを全部クリアしてしまうことにした。

「Windows 7でARPキャッシュをクリアする方法」については下記エントリーを参考にした。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://nymemo.com/windows/721/" title="windows7でarpのキャッシュを削除する（管理者権限のあるコマンドプロンプトの出し方） ： nymemo" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://nymemo.com/windows/721/" width="120" height="90" style="float: left;"></a><a href="http://nymemo.com/windows/721/" title="windows7でarpのキャッシュを削除する（管理者権限のあるコマンドプロンプトの出し方） ： nymemo" target="_blank">windows7でarpのキャッシュを削除する（管理者権限のあるコマンドプロンプトの出し方） ： nymemo</a><a href="http://b.hatena.ne.jp/entry/http://nymemo.com/windows/721/"><img src="http://b.hatena.ne.jp/entry/image/http://nymemo.com/windows/721/" alt="はてなブックマーク - windows7でarpのキャッシュを削除する（管理者権限のあるコマンドプロンプトの出し方） ： nymemo" title="はてなブックマーク - windows7でarpのキャッシュを削除する（管理者権限のあるコマンドプロンプトの出し方） ： nymemo"></a><br style="clear: both;" /></div>

具体的には、管理者権限で起動したコマンドプロンプトで、下記コマンドを実行するだけ。

{% highlight sh %}
> arp -d
{% endhighlight %}

クリアできたことは、`arp -a`を実行すれば確認できる。

# Remoteアプリの再接続にトライ

ARPキャッシュをクリアすることで、iPhoneのRemoteアプリが、PC上のiTunesを認識してくれるようになった。

その上で、再度Apple Watchから接続を試す。パスコードを入力すると…

[![iTunesがパスコードを通してくれた][3]][3]

  [3]: /images/pascode_accepted_on_iTunes.png

瞬時に接続が確立した！

[![iTunesがパスコードを通してくれた][4]][4]

  [4]: /images/Remote_on_Watch_connected.png

手首のWatchでiTunesを操作できるようになって、こりゃ便利！＼(^o^)／






