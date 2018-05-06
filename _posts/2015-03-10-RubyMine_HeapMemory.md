---
layout: post
title: RubyMineのヒープメモリを増やす
tags:
- RubyMine
---

RubyMine EAP (Satsuki)を使っていたんだけど、ヒープメモリが足りなくなってしまい、ファイルを編集するだけでも滞るようになってしまった。

# ググってみる

2015.2.3付(最終更新)のIntelliJ IDEA HELPが引っかかった。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="https://www.jetbrains.com/idea/help/increasing-memory-heap.html" title="Increasing Memory Heap" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?https://www.jetbrains.com/idea/help/increasing-memory-heap.html" width="120" height="90" style="float: left;"></a><a href="https://www.jetbrains.com/idea/help/increasing-memory-heap.html" title="Increasing Memory Heap" target="_blank">Increasing Memory Heap</a><a href="http://b.hatena.ne.jp/entry/https://www.jetbrains.com/idea/help/increasing-memory-heap.html"><img src="http://b.hatena.ne.jp/entry/image/https://www.jetbrains.com/idea/help/increasing-memory-heap.html" alt="はてなブックマーク - Increasing Memory Heap" title="はてなブックマーク - Increasing Memory Heap"></a><br style="clear: both;" /></div>

RubyMineじゃないけど、まぁココに書いてあることは参考にできるでしょ。IntelliJ IDEAでは`/Applications/IntelliJ IDEA.app/bin/idea.vmoptions`の中を書き換えればいいらしい。

# 書き換えた

上記パスに相当するファイルをRubyMine.appの中で探してみると、それらしきものがみつかった。

`/Applications/RubyMine EAP.app/Contents/bin/rubymine.vmoptions`

このファイルの中で`-Xmx512m`と設定されていた箇所を、`-Xmx1024m`に書き換えてみた。

![ヒープメモリ増量に成功](/images/RubyMine_HeapMemory_Changed.png)

成功！
