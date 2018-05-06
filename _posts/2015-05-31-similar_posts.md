---
layout: post
title: Jekyllの"Related Posts"に「最近の投稿」が表示される問題
tags:
- Jekyll
---

Jekyllの"Related Posts"(関連エントリー)は、プラグインが動作する環境が必要らしい。そして、そのようなプラグインが動作しないGitHub Pagesでは、うまく機能してくれない。

具体的には、「関連エントリー」を表示したいんだけど、単に日付の新しい「最近投稿したエントリー」が表示されてしまう。

# この問題に関する参考情報

下記ページで丁寧に解説してくれていた。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://zhangwenli.com/blog/2014/07/15/jekyll-related-posts-without-plugin/" title="Jekyll Related Posts without Plugin - 羡辙杂俎" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://zhangwenli.com/blog/2014/07/15/jekyll-related-posts-without-plugin/" width="120" height="90" style="float: left;"></a><a href="http://zhangwenli.com/blog/2014/07/15/jekyll-related-posts-without-plugin/" title="Jekyll Related Posts without Plugin - 羡辙杂俎" target="_blank">Jekyll Related Posts without Plugin - 羡辙杂俎</a><a href="http://b.hatena.ne.jp/entry/http://zhangwenli.com/blog/2014/07/15/jekyll-related-posts-without-plugin/"><img src="http://b.hatena.ne.jp/entry/image/http://zhangwenli.com/blog/2014/07/15/jekyll-related-posts-without-plugin/" alt="はてなブックマーク - Jekyll Related Posts without Plugin - 羡辙杂俎" title="はてなブックマーク - Jekyll Related Posts without Plugin - 羡辙杂俎"></a><br style="clear: both;" /></div>

# プラグイン無しで「関連エントリー」を表示するには

上記ページでは、そのタイトル通りに、プラグイン無しの「関連エントリー」表示方法まで記載してくれている。

具体的には、下記のコードをPostsに埋め込めばいい。

<script src="https://gist.github.com/satoyos/17b59bb858d81a069f83.js"></script>

このコードを`_includes/similar_posts.html`として保存して、それを`_layout/post.html`内からincludeすることで、関連エントリーを表示できるようになった。