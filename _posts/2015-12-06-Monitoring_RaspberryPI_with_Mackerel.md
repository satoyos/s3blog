---
layout: post
title: MackerelでRaspberry Piをモニタリング（死活監視）してみる
tags:
- Raspberry PI
- Mackerel
---

うちのRaspberry Pi2の仕事が増えてきた。もちろん、止まってもそれほど大した被害はないんだけど、「何か問題が起きたらすぐ分かるように監視できればなぁ」という程度にはモニタリングしたくなった。

ちょっと調べてみたら、はてなが提供している監視サービス[Mackerel](https://mackerel.io/instruction)に、Freeプランがあるようだ。僕のような趣味ベースのユーザーが試してみるには打ってつけじゃないの。

ということでRaspberry Pi2にMackerelのエージェトを導入して監視を始め、問題があったら家庭で使っているチャットツール[Slack](https://slack.com)に通知してもらうところまで試してみた。<br />
このエントリーはその作業メモで、分からないことは全てググって解決できたので、自分で新たに発見した知見は特にない。

# Mackerelの試用開始とエージェントの導入

基本的に、下記のDevelopers.IO記事を参考にしながら、サインアップと導入作業を進めてみた。約1年半前の記事で、今は少し違っている点もあるけれど、基本的な流れは全く変わらない。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa; margin: 0px 0px 50px;"><a href="http://dev.classmethod.jp/cloud/mackerel-firsttime/" title="Mackerelを始めてみた ｜ Developers.IO" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://dev.classmethod.jp/cloud/mackerel-firsttime/" width="120" height="90" style="float: left;"></a><a href="http://dev.classmethod.jp/cloud/mackerel-firsttime/" title="Mackerelを始めてみた ｜ Developers.IO" target="_blank">Mackerelを始めてみた ｜ Developers.IO</a><a href="http://b.hatena.ne.jp/entry/http://dev.classmethod.jp/cloud/mackerel-firsttime/"><img src="http://b.hatena.ne.jp/entry/image/http://dev.classmethod.jp/cloud/mackerel-firsttime/" alt="はてなブックマーク - Mackerelを始めてみた ｜ Developers.IO" title="はてなブックマーク - Mackerelを始めてみた ｜ Developers.IO"></a><br style="clear: both;" /></div>

## アカウント作成

上記記事の通りに進めた。

1. Mackerelのサインアップのページでメアドを登録する。
2. 登録したメアドにMackerelから「[Mackerel] Please verify your email」というタイトルのメールが送られてくるので、中のリンクをクリックする。
3. すると、パスワード設定ページが開くので、パスワードを指定する。これでアカウントは作成できた。
4. その後の手順は、全部Mackerelが1ステップずつ導いてくれるので、organizationの設定から一つずつ進めていけばOK。

## 要注意点：エージェントの導入

ここでは、上記手順の4の中で一カ所だけ躓いた「Mackerelエージェントの導入」について、少し丁寧に書いておく。<br />
Mackerelで監視を行うには、監視対象のサーバに「エージェント」をインストールする必要がある。

まずは、Mackerelのガイダンス通りにダウンロードとインストールを進めてみる。

### 方法1: apt-get → 失敗

二通りやり方が書いてあるうちの一つ目、`apt-get`による導入を試してみた。

{% highlight sh %}
$ sudo apt-get install mackerel-agent
Reading package lists... Done
Building dependency tree
Reading state information... Done
E: Unable to locate package mackerel-agent
{% endhighlight %}

ありゃ、対応パッケージが無いらしい。きっと、Raspberry PiのCPUであるARM用のパッケージが用意されてないのね。

### 方法2: 手作業でダウンロードとインストール → 失敗

ということで、もう一つの方法である「手作業」の手順を踏んでいく。

{% highlight sh %}
$ curl -O https://mackerel.io/file/agent/deb/mackerel-agent_latest.all.deb
$ sudo dpkg -i mackerel-agent_latest.all.deb
Selecting previously unselected package mackerel-agent.
(Reading database ... 21104 files and directories currently installed.)
Preparing to unpack mackerel-agent_latest.all.deb ...
Unpacking mackerel-agent (0.25.1-1) ...
Setting up mackerel-agent (0.25.1-1) ...
 Adding system startup for /etc/init.d/mackerel-agent ...
   /etc/rc0.d/K20mackerel-agent -> ../init.d/mackerel-agent
   /etc/rc1.d/K20mackerel-agent -> ../init.d/mackerel-agent
   /etc/rc6.d/K20mackerel-agent -> ../init.d/mackerel-agent
   /etc/rc2.d/S20mackerel-agent -> ../init.d/mackerel-agent
   /etc/rc3.d/S20mackerel-agent -> ../init.d/mackerel-agent
   /etc/rc4.d/S20mackerel-agent -> ../init.d/mackerel-agent
   /etc/rc5.d/S20mackerel-agent -> ../init.d/mackerel-agent
Processing triggers for ureadahead (0.100.0-16) ...
$ sudo sed -i.bak '1s:^:apikey = "### 割り当てられたAPI-KEY###"\'$'\n'':' /etc/mackerel-agent/mackerel-agent.conf
{% endhighlight %}

ここまでは問題無かった。ただ、次の手順でエージェントをstartさせようとすると、失敗した。

{% highlight sh %}
$ sudo /etc/init.d/mackerel-agent start
 * Starting mackerel-agent     <空白略>         [fail]
{% endhighlight %}

ログ`/var/log/mackerel-agent.log`の内容を確認してみる。

{% highlight sh %}
/usr/local/bin/mackerel-agent: 1: /usr/local/bin/mackerel-agent: Syntax error: "(" unexpected
{% endhighlight %}
うーん、手作業でもARM対応のエージェントをインストールできてない、ということかな
？

### ARM対応版のファイルを入手して解決！

ググると、すぐに同じ問題について書き残してくれている記事が見つかった。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://blog.hidekiy.com/2015/07/raspberrypi-mackerel-agent.html" title="hidekiy blog: [raspberrypi] mackerel-agent を簡単に導入する" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://blog.hidekiy.com/2015/07/raspberrypi-mackerel-agent.html" width="120" height="90" style="float: left;"></a><a href="http://blog.hidekiy.com/2015/07/raspberrypi-mackerel-agent.html" title="hidekiy blog: [raspberrypi] mackerel-agent を簡単に導入する" target="_blank">hidekiy blog: [raspberrypi] mackerel-agent を簡単に導入する</a><a href="http://b.hatena.ne.jp/entry/http://blog.hidekiy.com/2015/07/raspberrypi-mackerel-agent.html"><img src="http://b.hatena.ne.jp/entry/image/http://blog.hidekiy.com/2015/07/raspberrypi-mackerel-agent.html" alt="はてなブックマーク - hidekiy blog: [raspberrypi] mackerel-agent を簡単に導入する" title="はてなブックマーク - hidekiy blog: [raspberrypi] mackerel-agent を簡単に導入する"></a><br style="clear: both;" /></div>

この記事によると、

> この状態だと設定ファイル類は正しく存在するのですが肝心のバイナリが i386 になっていて正常に動作しません。

とのこと。やっぱりか。<br />
解決方法も書いてくれてあったので、それをなぞる。

まずは、[GitHubのリポジトリ](https://github.com/mackerelio/mackerel-agent/releases/latest)から、Linux用、かつARM用の`mackerel-agent_linux_arm.tar.gz`ファイルをダウンロードする。

これを解凍し、`file`コマンドでエージェントファイルを確認。

{% highlight sh %}
$ file mackerel-agent_linux_arm/mackerel-agent
mackerel-agent_linux_arm/mackerel-agent: ELF 32-bit LSB  executable, ARM, EABI5 version 1 (SYSV), statically linked, not stripped
{% endhighlight %}

よしよし。これでエージェントファイルを上書きし、startしてみる。

{% highlight sh %}
$ sudo cp mackerel-agent_linux_arm/mackerel-agent /usr/local/bin
$ sudo /etc/init.d/mackerel-agent start
 * Starting mackerel-agent     <空白略>         [ OK ]
{% endhighlight %}

起動に成功した。ログ`/var/log/mackerel-agent.log`の内容も再度確認しておく。

{% highlight sh %}
2015/12/06 11:28:01 INFO <main> Starting mackerel-agent version:0.25.1, rev:4076142, apibase:https://mackerel.io
2015/12/06 11:28:03 INFO <command> Start: apibase = https://mackerel.io, hostName = raspi2, hostID = ##foobar##
{% endhighlight %}

よろしいでしょう！<br />
あとは、Mackerelのガイダンス通りに進めていけば、ノートラブルでモニタリングを開始できた。

[![MackerelでRaspberry Pi2の監視を開始！][1]][1]

  [1]: /images/Mackerel_start_monitoring.png


監視対象のサーバがどういうものなのか、についてもこのダッシュボードから参照できる。

[![MackerelでRaspberry Pi2の監視を開始！][3]][3]

  [3]: /images/Monitoring_my_raspi2.png

うん、このスペックは、まさにうちのRaspberry Pi2であります！

# Slackへの通知設定

Mackerelデフォルトの通知先は、アカウント作成時に登録したメールアドレスになっている。<br />
サービス運営からの「お知らせ」はメールで受け取りたいけど、問題が起きたときの通知（アラート）はSlackの方で受け取りたい…と思ったので、その設定をしてみた。

といっても方法は簡単で、Mackerelのヘルプに丁寧な説明が用意されているので、その手順をなぞっただけ。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://help-ja.mackerel.io/entry/howto/alerts/slack" title="Slackにアラートを通知する - Mackerel ヘルプ" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://help-ja.mackerel.io/entry/howto/alerts/slack" width="120" height="90" style="float: left;"></a><a href="http://help-ja.mackerel.io/entry/howto/alerts/slack" title="Slackにアラートを通知する - Mackerel ヘルプ" target="_blank">Slackにアラートを通知する - Mackerel ヘルプ</a><a href="http://b.hatena.ne.jp/entry/http://help-ja.mackerel.io/entry/howto/alerts/slack"><img src="http://b.hatena.ne.jp/entry/image/http://help-ja.mackerel.io/entry/howto/alerts/slack" alt="はてなブックマーク - Slackにアラートを通知する - Mackerel ヘルプ" title="はてなブックマーク - Slackにアラートを通知する - Mackerel ヘルプ"></a><br style="clear: both;" /></div>

家庭内チャットツールとして使っているSlackの中で、僕だけが開発用に読み書きしている`#dev`チャネルにメッセージを送ってもらうように設定してみた。<br />
テストしてみると…

[![MackerelからSlackに通知を投げてみるテスト][2]][2]

  [2]: /images/Mackerel_to_Slack.png

できた！

## サーバをわざと落としてみる

あとは、何かトラブルが起きるのを待つのみ？自分でRaspberry PiへのUSB給電を引っこ抜いてみるか？(^^;;)

いきなり引っこ抜くのはさすがに乱暴なので、おとなしくシャットダウンしてから電源を断ったら…<br />
Slackに通知キタ━━━━(゜∀゜)━━━━━!!! <br />
しかも、なんかサーバを見失うまでのグラフまで付いてる。(笑)

[![Mackeralからサーバロストの通知][4]][4]

  [4]: /images/Lost_message_of_Mackerel.png

それから、USB給電を復活させた。すると、すぐに「復活しました」通知も来た。

[![Mackeralからサーバ復活の通知][5]][5]

  [5]: /images/Recover_message_of_Mackerel.png

いいね！ちゃんと機能してるじゃないの。

# まとめ

デフォルトのパッケージにはARM用のエージェントが入っておらず、それをGitHubからダウンロードするところだけ躓いたけど、それを除けばスムーズに監視を開始できた。良くできているなぁ。

うちのRaspberry Piがどういう状況なのか、これまで可視化はおろか確認もしたことがなかったので、今後はMackerelのダッシュボードで時々チェックしてみようと思う。楽しみ！(^^)/



