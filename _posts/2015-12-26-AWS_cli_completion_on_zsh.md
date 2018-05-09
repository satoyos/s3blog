---
layout: post
title: aws-cliのコマンドをzsh上で補完したい
tags:
- AWS
- Mac OS X
---

Mac（OS X El Capitan）のzsh上でaws-cliのコマンドでTAB補完を有効にしたくて、下記記事などを参考に…

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="http://qiita.com/ainoya/items/9f6dce896deab77a001e" title="zshでaws-cliのコマンド補完を利用する - Qiita" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?http://qiita.com/ainoya/items/9f6dce896deab77a001e" width="120" height="90" style="float: left;"></a><a href="http://qiita.com/ainoya/items/9f6dce896deab77a001e" title="zshでaws-cliのコマンド補完を利用する - Qiita" target="_blank">zshでaws-cliのコマンド補完を利用する - Qiita</a><a href="http://b.hatena.ne.jp/entry/http://qiita.com/ainoya/items/9f6dce896deab77a001e"><img src="http://b.hatena.ne.jp/entry/image/http://qiita.com/ainoya/items/9f6dce896deab77a001e" alt="はてなブックマーク - zshでaws-cliのコマンド補完を利用する - Qiita" title="はてなブックマーク - zshでaws-cliのコマンド補完を利用する - Qiita"></a><br style="clear: both;" /></div>

{% highlight bash %}
source /usr/local/bin/aws_zsh_completer.sh
{% endhighlight %}

を`~/.zshrc`に加えてみたんだけど、私の環境ではなぜかシェルごと落ちてしまい、補完を有効にできないままだった。

しかし、今日（2015/12/26）たまたま`brew upgrade`でaws-cliのバージョンを1.9.15に上げたら、なんかメッセージが表示された。

{% highlight sh %}
The "examples" directory has been installed to:
  /usr/local/share/awscli/examples

Add the following to ~/.bashrc to enable bash completion:
  complete -C aws_completer aws

Add the following to ~/.zshrc to enable zsh completion:
  source /usr/local/share/zsh/site-functions/_aws
{% endhighlight %}

これかも！最終行の

{% highlight bash %}
source /usr/local/share/zsh/site-functions/_aws
{% endhighlight %}

を`~/.zshrc`に追加すると、無事にaws-cliコマンドで補完が効くようになりました！