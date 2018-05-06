---
layout: post
title: 【Appium 1.4→1.5移行】iOSシミュレータが2回起動する問題
tags:
- Appium
---

この記事は、以下の記事の続きになります。iOSアプリのE2Eテストに利用しているフレームワーク「Appium」のバージョンアップ(1.4→1.5)に関する話題です。

前記事 →[【Appium 1.4→1.5移行】:nameでUI部品を指定できなくなった問題][1]

  [1]: /2016/08/28/Migration-to-Appium1.5/

# テストは動くようになったけど…

[前回の記事][1]で、無事にAppium 1.5のテストが動くようになったのはいいんだけど、一つだけ問題が残った。

Appiumのテストでは、ひとかたまりのテストごとにシミュレータが再起動する。<br />
例えば、テストコードをRSpec形式で書く場合には、最上位の`describe`ごとに再起動する。

問題というのは、この再起動が**必ず2回走るようになった**ということ。<br />
順を追って状況を書くと、次のようになる。

1. Appiumサーバが、シミュレータをブートしようとする。(←起動1回め)
2. 失敗する。
3. Appiumサーバが、シミュレータを再起動する。(←起動2回め)
4. 今度は起動に成功し、テストコードを実行できる。

Appiumサーバのログで言うと、このあたり。

{% highlight ruby %}
....
[debug] [iOS] User specified default device, letting instruments launch it
[debug] [iOS] Running ios sim reset flow
[debug] [iOS] Killing the simulator
[debug] [iOSSim] Killing all iOS Simulators
[debug] [iOS] Cleaning sim state.
....
{% endhighlight %}

うまく起動せずに、iOSシミュレータをリセットしているみたい。


# 解決策

この問題も、困っているのは僕だけじゃないだろう、と思ってググったら、やっぱり他にも困っている人がいて、Appium本家のフォーラムで相談していた。

[Appium starts iOS simulator TWICE for every test](https://discuss.appium.io/t/appium-starts-ios-simulator-twice-for-every-test/9303)

そして、このスレッドの最後に解決策が提示されていた。

>With Appium 1.5.3 this is the fix:
>
>>Setting the "fullReset: true" capability fixed this for me

これはいいニュース！ `spec_helper.rb`で行っている全体設定に追記してみる。<br />

{% highlight ruby %}
def desired_caps
  {
      caps: {
          platformName:  "iOS",
          versionNumber: "9.2",
          deviceName:    "iPhone 5s",
          app: '../build/iPhoneSimulator-9.0-Development/Shuffle100.app',
          fullReset: true,  # ←ここに追加！！！
      },
      appium_lib: {
          wait: 10
      }
  }
end
{% endhighlight %}

これで、「シミュレータの2回起動」を回避できるようになった。