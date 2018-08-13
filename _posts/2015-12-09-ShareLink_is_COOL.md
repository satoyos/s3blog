---
layout: post
title: ブックマークレット"ShareLink"が便利
tags:
- Jekyll
---

Webページへのリンクをブログに埋め込む際に、いつもタイトルとURLをそれぞれコピペしてリンクを作ってたんだけど…<br />
ブックマークレット「ShareLink」を使えば、サムネイル付きの見栄えのよいリンクが簡単にできる、ということを今更ながら知った。

こんな感じ↓でリンクができる。
<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://bloblo.jp/2015/10/14" title="ブロガー必見！サムネイル付きリンク作成ブックマークレット「ShareLink」を作ってみた | ぶろぶろ！" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://bloblo.jp/2015/10/14" width="120" height="90" style="float: left;"></a><a href="http://bloblo.jp/2015/10/14" title="ブロガー必見！サムネイル付きリンク作成ブックマークレット「ShareLink」を作ってみた | ぶろぶろ！" target="_blank">ブロガー必見！サムネイル付きリンク作成ブックマークレット「ShareLink」を作ってみた | ぶろぶろ！</a><a href="http://b.hatena.ne.jp/entry/http://bloblo.jp/2015/10/14"><img src="http://b.hatena.ne.jp/entry/image/http://bloblo.jp/2015/10/14" alt="はてなブックマーク - ブロガー必見！サムネイル付きリンク作成ブックマークレット「ShareLink」を作ってみた | ぶろぶろ！" title="はてなブックマーク - ブロガー必見！サムネイル付きリンク作成ブックマークレット「ShareLink」を作ってみた | ぶろぶろ！"></a><br style="clear: both;" /></div>

このページで紹介されているブックマークレットをそのまま使うと、私個人の感覚では「もうちょっとリンク下のパーツとの間隔を空けたい」と思ったので、styleに`margin`を追加して使わせてもらっている。

{% highlight javascript %}
javascript:(function(){var%20w%20=%20120,%20h%20=%2090,%20u%20=%20location.href,%20t%20=%20document.title;var%20a%20=%20'<a%20href="'+u+'"%20title="'+t+'"%20target="_blank">',%20ca%20=%20'</a>';var%20img%20=%20'<img%20src="http://capture.heartrails.com/'%20+w+'x'+h+'/shadow?'+u+'"%20width="'+w+'"%20height="'+h+'"%20style="float:%20left;">';var%20hatena%20=%20'<a%20href="http://b.hatena.ne.jp/entry/'%20+u+'"><img%20src="http://b.hatena.ne.jp/entry/image/'%20+u+'"%20alt="はてなブックマーク%20-%20'+t+'"%20title="はてなブックマーク%20-%20'+t+'"></a>';var%20tag%20=%20'<div%20class="sharelink"%20style="padding:%206px;%20border:%201px%20solid%20#aaaaaa;%20%20margin:%200px%200px%2050px;">'%20+%20a%20+%20img%20+%20ca%20+%20a%20+%20t%20+%20ca%20+%20hatena%20+%20'<br%20style="clear:%20both;"%20/></div>';prompt('ShareLink',%20tag);})();
{% endhighlight %}
