---
layout: post
title: AppiumテストでUIPickerViewの値を選ぶ
tags:
- Appium
---

[iOSアプリ](https://github.com/satoyos/Shuffle100)をRubyMotionで書いている。<br>
E2Eテストのフレームワークには、これまで[Frank](http://www.testingwithfrank.com/)を使ってきたが、ここ半年ぐらい更新されておらず、Xcode7＋iOS9には追随できていないらしい。<br>
そこで、「AndroidもiOSもテストできて、テストを記述する言語も色々選べる」と噂のフレームワーク*Appium*に乗り換えつつある。

今回は、AppiumでUIPickerViewの値を選ぶ方法について試行錯誤したので、その経緯を備忘録的に書き残す。

# テストした環境

- OS: Mac OS X Yosemite
- appium: 1.4.11
- Appium.app: 1.4.8
- Xcode: 7.0
- シミュレータのiOSバージョン: 9.0

# 操作すべきエレメントは？

Appium.appで調べてみたら、UIPickerViewの値を管理しているエレメントは`UIAPickerWheel`らしい。

[![操作対象のエレメント調査][1]][1]

  [1]: /images/UIAPickerWheel.jpg

# 値を変更するメソッドは？

ググってみたけれど、情報が錯綜していた。

### selectValue

まず試したのは、`selectValue`。

{% highlight ruby %}
  wheel = find_element(class_name: 'UIAPickerWheel')
  wheel.selectValue 'いなばくん（人間）'
{% endhighlight %}
しかし、これは以下のようにあえなくNoMethodError。

{% highlight sh %}
     Failure/Error: wheel.selectValue 'いなばくん（人間）'
     NoMethodError:
       undefined method `selectValue' for #<Selenium::WebDriver::Element:0x..fe29c9ad12120222e id="9">
{% endhighlight %}

### send_keys

次に見つかったのが、`send_keys`。

{% highlight ruby %}
  wheel.send_keys 'いなばくん（人間）'
{% endhighlight %}

このメソッドで、うまくPickerの値を変更することができた。

