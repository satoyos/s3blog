---
layout: post
title: Xcode7-beta環境でのビルドに失敗した際の対処メモ
tags:
- RubyMotion
- Xcode
---

RubyMotionで作っているiOSアプリが、iOS9(beta)でもきちんと動くかどうかを確認するため、Xcode7-beta環境でビルドしようとしたところ、エラーが出てビルドに失敗した。<br>
そこで、エラーメッセージを見ながら対策を調べてみた。

# 症状

まず、ビルドにiOS9用のSDK（Xode-beta）を利用するため、`Rakefile`内で次のように設定した。

{% highlight ruby %}
...
Motion::Project::App.setup do |app|
  ...
  app.xcode_dir = "/Applications/Xcode-beta.app/Contents/Developer"
  app.deployment_target = "9.0"
  app.sdk_version = "9.0"
  ...
end
{% endhighlight %}

そして、`rake`コマンドでビルドしてみる。

{% highlight sh %}
$ rake
     Build ./build/iPhoneSimulator-9.0-Development
    ERROR! Building vendor project `vendor/Pods' failed to create at least one `.a' library.
{% endhighlight %}

エラーだ。まずCocoaPodsのライブラリをビルドしようとして失敗しているっぽいけど、これだけでは何が悪いのかよく分からない。<br>
なので、`rake --trace`でもう少し詳しくエラーを見てみる。

{% highlight sh %}
$ rake --trace
...
xcodebuild: error: SDK "iphonesimulator9.0" Cannot be located.
...
{% endhighlight %}

これだ。xcodebuildコマンドが、iOS9(beta)のSDKがどこにあるか分からない、ということらしい。

要は、Xcode7-betaのxcodebuildではなく、Xcode6の同コマンドが動いている、ということなのだろう。

# 対策

では、Xcode7-beta環境のxcodebuildを動かすにはどうすれば？とググってみたら、やっぱりあった。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="https://github.com/Carthage/Carthage/issues/536" title="To work with Xcode 7 and Swift 2 supported frameworks, before `carthage *` run `xcode-select` · Issue #536 · Carthage/Carthage" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?https://github.com/Carthage/Carthage/issues/536" width="120" height="90" style="float: left;"></a><a href="https://github.com/Carthage/Carthage/issues/536" title="To work with Xcode 7 and Swift 2 supported frameworks, before `carthage *` run `xcode-select` · Issue #536 · Carthage/Carthage" target="_blank">To work with Xcode 7 and Swift 2 supported frameworks, before `carthage *` run `xcode-select` · Issue #536 · Carthage/Carthage</a><a href="http://b.hatena.ne.jp/entry/https://github.com/Carthage/Carthage/issues/536"><img src="http://b.hatena.ne.jp/entry/image/https://github.com/Carthage/Carthage/issues/536" alt="はてなブックマーク - To work with Xcode 7 and Swift 2 supported frameworks, before `carthage *` run `xcode-select` · Issue #536 · Carthage/Carthage" title="はてなブックマーク - To work with Xcode 7 and Swift 2 supported frameworks, before `carthage *` run `xcode-select` · Issue #536 · Carthage/Carthage"></a><br style="clear: both;" /></div>

ここのアドバイスよると、次のコマンド一発で解決できる、とのこと。

{% highlight sh %}
$ sudo xcode-select -s /Applications/Xcode-beta.app/Contents/Developer
{% endhighlight %}

このコマンドを実行してみたら…あっさりとiOS9(beta)のSDKでビルドできた！＼(^o^)／
