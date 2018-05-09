---
layout: post
title: Raspberry PI2で定期的にHubotを起こす
tags:
- Ubuntu
- Raspberry PI
---

うちの[Hubot](https://hubot.github.com/)は30分間アクセスがないと寝てしまうので、常時起動している[Raspberry PI2](https://www.raspberrypi.org/products/raspberry-pi-2-model-b/)から20分に1回アクセスして寝かせないようにした。<br>
要は、UNIX（Linux）のcrontabを初めて書いてみた、という話です。

# 家庭内Slackはじめました

エンジニア界隈では「いまどきメールでの情報共有は無いわー」とされていて、[Slack](https://slack.com/)などのチャットサービスで情報共有し、ついでにプロダクトの自動デプロイまでやっちゃうらしい。

僕もそんなチャットサービスを使ってみたいんだけど、職場ではSlackのような社外サービスを使うことが禁止されている。

そこで、LINEすら使っていないうちの家庭内の情報共有ツールとして、Slackを導入してみた。結果は上々。

[![Slackによる家庭内情報共有][1]][1]

  [1]: /images/slack_in_my_family.jpg

急にスマホにSlackアプリをインストールさせられ、アカウントを作ることになった家族は、迷惑な思いをしたに違いない。<br>
最初は「何それ使えんの？」と半信半疑だったけど、一週間と経たないうちに「じゃあ、○○はSlackに貼っておくからね」とごく普通に使い始めた。<br>
「Slackに書いたり貼ったりしておけば、家族に見てもらえる。そして記録に残る」という嬉しさは、家庭内Slackでも十分に享受できる。

- 旅行の準備
- 旅行中の状況報告
- 買ってきてほしいものメモ
- その他雑談

など、いろんな用途に使える。

# どうせならHubotも

Slackによる情報共有の便利さは体感できた。

**次は、「Slackからプロダクトをデプロイ」とかカッコいいこともしてみたい！**

という訳で、[bouzuyaさんのQiita連載](http://qiita.com/bouzuya/items/c7d0ad80c357aab6b696)を参考に、Hubotを[Heroku](https://www.heroku.com/)で動かし、Slackと連携させるところまで作業した。

[![Slackによる家庭内情報共有][2]][2]

  [2]: /images/slack_with_hubot.jpg

Hubot導入の手順については、上記のbouzuyaさんの懇切丁寧な説明をなぞっただけなので、ここではパス！（bouzuyaさん、本当にありがとうございます。）

家族も、

{% highlight sh %}
@hubot: img NMB48
{% endhighlight %}

とか打ち込んで、楽しむようになった。

# しかし、Hubotは寝る。

正確には、Herokuで無料で動かすWebアプリには、制約がある。

1. 30分間アクセスがないと、寝る。
2. 無料で動かすアプリは、一日に6時間はまとめて寝る必要がある。（1日中起こしっぱなしにはできない）

「趣味で使う範囲ならココまでね」という制約としては、十分受け入れられる、見事な条件設定。

しかし、寝てしまったHubotをいちいち起こすのは面倒。なので、うちで24時間稼働しているマシンのうち、Gmailのメールフィルタリングに使っているRaspberry PI2を「Hubot起こし係」に仕立てることにした。

（なお、Raspberry PI2にUbuntuをインストールして、メールフィルタリング・ソフトを動かすまでの道のりについては、こちらからどうぞ。→ [Raspberry PIで遊ぶ](/pages/Raspberry_Pi2)）

# cronを設定する

さて、ここからがようやく本題。

使ったことないけど、UNIXには「cron」という仕組みがあり、それを使えば指定したタイミングで指定したプログラムを実行できる、ということは知っていた。

あとはそれを勉強して、実際に書いてみるだけだ！という訳で、この解説記事↓を参考にした。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://www.server-memo.net/tips/crontab.html" title="crontabの書き方 | server-memo.net" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://www.server-memo.net/tips/crontab.html" width="120" height="90" style="float: left;"></a><a href="http://www.server-memo.net/tips/crontab.html" title="crontabの書き方 | server-memo.net" target="_blank">crontabの書き方 | server-memo.net</a><a href="http://b.hatena.ne.jp/entry/http://www.server-memo.net/tips/crontab.html"><img src="http://b.hatena.ne.jp/entry/image/http://www.server-memo.net/tips/crontab.html" alt="はてなブックマーク - crontabの書き方 | server-memo.net" title="はてなブックマーク - crontabの書き方 | server-memo.net"></a><br style="clear: both;" /></div>

## Hubotを起こすスクリプトを書く

まずは、Heroku上のHubotアプリにアクセスするだけのシェルスクリプト`~/crons/hubot-wake-up`を書いた。

{% highlight sh %}
#!/bin/sh

/usr/bin/curl https://[Hubot用サブドメイン].herokuapp.com
{% endhighlight %}

`chmod`で実行権限を有効にして、このスクリプトを動かしてみる。

{% highlight sh %}
ubuntu@raspi2:~$ ./crons/hubot-wake-up
Cannot GET /
{% endhighlight %}

うん、とりあえずはHerokuのHubotサービスに繫がったっぽい。

## 定期的にスクリプトを実行するcronを設定

次に、作ったばかりのシェルスクリプトを定期的に呼び出すcron設定（crontab）を記述する。

具体的には、`crontab -e`コマンドで、そのユーザ用のcrontabファイルの編集画面が開く。<br>
初期状態ではコメント文しかないので、上記スクリプトを呼び出すスケジュール設定を書く。

{% highlight sh %}
# Edit this file to introduce tasks to be run by cron.
#
# 〜最初から埋まっていたコメント文は中略！
#
# For more information see the manual pages of crontab(5) and cron(8)
#

MAILTO=""

# m h  dom mon dow   command
10,30,50 0,7-23 * * * /home/ubuntu/crons/hubot-wake-up
{% endhighlight %}

この設定のポイントは、次の通り。

- どうもcronはメッセージをメールで飛ばそうとするらしい。そして、メールを送信できる環境を整えておかないと、いちいち`/var/log/syslog`に  
{% highlight sh %}
(CRON) info (No MTA installed, discarding output)
{% endhighlight %}

という情報が出力される。害はないかもしれないけれど、ときどきチェックしたいログの量が無用に増えるのもイヤなので、  
{% highlight sh %}
MAILTO=""
{% endhighlight %}
と設定した。これにより、上記の「MTAうんぬん」情報はログに出なくなった。
- Hubotに、毎時10分,30分,50分の3回アクセスしてやることで、「30分アクセスがないとHubotが寝る」事態を回避する。  
そのため、「分」フィールドは`10,30,50`と設定した。
- ただし、Heroku上で無料で動かすアプリが守るべきルールとして、「1日6時間は連続して寝ること」という条件を守らねば！
なので、「時」フィールドは`0,7-23`と設定した。  
これにより、「0時台と、7〜23時台にのみ実行」と指定したことになる。

# おわりに

初めてcron（crontab）を設定してみたけど、`/var/log/syslog`で確認してみる限り、ちゃんと指定したスケジュールでスクリプトが実行されている。

おかげで、家族が起きている時間帯は、いつ呼びかけてもHubotがすぐに返事を返してくれる。

[![いつもすぐに返事してくれるHubot][3]][3]

  [3]: /images/hubot-ping-pong.png

これで、うちのRaspberry PI2の任務は2つに増えた。

1. 30秒ごとにGmailの「受信箱」に入っているメールを分類する（←POPFile）
2. 家族が起きている時間帯は、Hubotを寝かさないようにする（←シェルスクリプト＋crontab）

5,000円のマシンですが、いい仕事してます！＼(^o^)／

