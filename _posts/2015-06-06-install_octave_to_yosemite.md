---
layout: post
title: Max OS X Yosemiteに octave をインストールする
tags:
- Mac OS X
- Octave
- MachineLearning
---

[Coursera の Machine Learningの講義](https://www.coursera.org/course/ml)で使うプログラム octave をインストールしようとして、つまづいた。

# HomeBrewでインストール

下記のQiitaエントリーを参考に、HomeBrewでインストールしてみた。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://qiita.com/ykoga/items/614c81a9d679b86ca96a" title="OctaveをMac OS X Yosemiteへインストール - Qiita" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://qiita.com/ykoga/items/614c81a9d679b86ca96a" width="120" height="90" style="float: left;"></a><a href="http://qiita.com/ykoga/items/614c81a9d679b86ca96a" title="OctaveをMac OS X Yosemiteへインストール - Qiita" target="_blank">OctaveをMac OS X Yosemiteへインストール - Qiita</a><a href="http://b.hatena.ne.jp/entry/http://qiita.com/ykoga/items/614c81a9d679b86ca96a"><img src="http://b.hatena.ne.jp/entry/image/http://qiita.com/ykoga/items/614c81a9d679b86ca96a" alt="はてなブックマーク - OctaveをMac OS X Yosemiteへインストール - Qiita" title="はてなブックマーク - OctaveをMac OS X Yosemiteへインストール - Qiita"></a><br style="clear: both;" /></div>

記事にあるように、初期状態のHomeBrewのままだと、octaveはインストールできない。

{% highlight sh %}
$ brew install octave
Error: No available formula for octave 
Searching formulae...
Searching taps...
homebrew/science/octave     Caskroom/cask/octave    Caskroom/cask/octave
{% endhighlight %}

そこで、記事にあるように、`homebrew/science`をtapしてからインストールしてみる。

{% highlight sh %}
$ brew tap homebrew/science
$ brew install octave
{% endhighlight %}

これでインストールできた。

# octaveの起動に失敗する

でも、`octave`コマンドを実行すると、エラーが出てしまう。

{% highlight sh %}
$  octave
dyld: Library not loaded: /usr/local/lib/gcc/5/libgfortran.3.dylib
  Referenced from: /usr/local/Cellar/octave/3.8.2_1/bin/octave-cli-3.8.2
  Reason: image not found
[1]    18339 trace trap  octave
{% endhighlight %}

どうも、`/usr/local/lib/gcc/5/`というフォルダの下を探そうとしているんだけど、そんなフォルダは今の環境には無い。その代わり、`/usr/local/lib/gcc/4.9/`ならある。

{% highlight sh %}
$  ls /usr/local/lib/gcc
4.9
{% endhighlight %}

# 対策1

ググってみると、同じような問題はRのインストール時にも起きているようで、例えば下記記事などが参考になった。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://potametabo.com/blog/2014/12/homebrewr.html" title="homebrewでRをインスコ - ポタメタボ" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://potametabo.com/blog/2014/12/homebrewr.html" width="120" height="90" style="float: left;"></a><a href="http://potametabo.com/blog/2014/12/homebrewr.html" title="homebrewでRをインスコ - ポタメタボ" target="_blank">homebrewでRをインスコ - ポタメタボ</a><a href="http://b.hatena.ne.jp/entry/http://potametabo.com/blog/2014/12/homebrewr.html"><img src="http://b.hatena.ne.jp/entry/image/http://potametabo.com/blog/2014/12/homebrewr.html" alt="はてなブックマーク - homebrewでRをインスコ - ポタメタボ" title="はてなブックマーク - homebrewでRをインスコ - ポタメタボ"></a><br style="clear: both;" /></div>

ここでは、存在しないdylbを呼び出そうとしている元ファイルを、`install_name_tool`コマンドを使って書き換えている。

そこで、僕も真似してみたんだけど、今回は書き換えが1つのファイルだけでは済まずに、他のファイルでもいちいち`nstall_name_tool`フォルダの下を見に行こうとしていることが分かった。

これだと、何個のファイルを書き換えれば済むのか分からないよ…。orz

# 対策2

もう、安直な対策で片付けることにした。存在しない`/usr/local/lib/gcc/5/`のシンボリックリンクを作って、存在する`/usr/local/lib/gcc/4.9/`へと誘導する。

{% highlight sh %}
$ sudo ln -s /usr/local/lib/gcc/4.9 /usr/local/lib/gcc/5
Password:
$ ls /usr/local/lib/gcc
4.9 5
{% endhighlight %}

シンボリックリンクが張れたので、Octaveを実行してみる。

{% highlight sh %}
$ octave
GNU Octave, version 3.8.2
Copyright (C) 2014 John W. Eaton and others.
This is free software; see the source code for copying conditions.
There is ABSOLUTELY NO WARRANTY; not even for MERCHANTABILITY or
FITNESS FOR A PARTICULAR PURPOSE.  For details, type 'warranty'.

Octave was configured for "x86_64-apple-darwin14.3.0".

Additional information about Octave is available at http://www.octave.org.

Please contribute if you find this software useful.
For more information, visit http://www.octave.org/get-involved.html

Read http://www.octave.org/bugs.html to learn how to submit bug reports.
For information about changes from previous versions, type 'news'.

octave:1>
{% endhighlight %}

うまくいった！
