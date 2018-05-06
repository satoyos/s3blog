---
layout: post
title: 【Appium 1.4→1.5移行】:nameでUI部品を指定できなくなった問題
tags:
- Appium
---

# 1. Appium 1.5に移行する理由

## これまでの経緯

[iOSアプリ「百首読み上げ」](/apps/shuffle100/)をリリースし、メンテナンスしている。<br />
E2Eテストのフレームワークには、ここ1年ほど[Appium](http://appium.io)（Ver.1.4）を使ってきた。Seleniumがベースになっていて、メンテナンスも（少なくとも他のフレームワークと比べて）活発に行われているので、しばらくは頼らせてもらえそうな印象を抱いている。

そのAppiumが、性能向上のためにかなり根本的に書き直され、Ver.1.5にバージョンアップしたことは知っていた。…

- しばらくはバグが多かった(らしい)。
- UI部品を指定するために多用している「:nameによるElement指定」が1.5からは使えなくなる

という理由により、ずっと1.5を使うことを避けていた。

## このままだと、iOS10(Xcode8)への対応が危うい

しかし、iOS10のリリースを控え、Xcode8(beta6)でビルドしたアプリをAppium1.4でテストしようとしたところ、iOSシミュレータが起動しなくなった。

ググってみたところ、最新版のAppium 1.5.3でもまだXcode8に対応できておらず、現在対応を進めているらしい。

[iOS 10 support · Issue #6597 · appium/appium](https://github.com/appium/appium/issues/6597)

1.5.3でもまだ対策中ならば、1.4を使い続けようとしても、その旧バージョンがXcode8対応になることは期待できそうにない。<br />
そう考えた末にやっと重い腰を上げて、E2EテストのコードをAppium 1.5対応にすることを検討し始めた。

# 2. 「:nameを指定したUI部品の取得」が不可になる

E2Eテストとして、「○○という操作をした結果、アプリの画面にxxxという文字列が見えていればOK」というテストコードをたくさん書いてきた。

例えば、私の百人一首アプリでは、`アプリを起動したときに、"百首読み上げ"という文字列が表示されていたら、ホーム画面の表示に成功したものとする`というテストを次のように書いている。

{% highlight ruby %}
# テストコード(一部抜粋)
describe '初心者モードのテスト' do
  it 'アプリのタイトルが正しく表示される' do
    can_see("百首読み上げ")  # 下記メソッドを呼び出す
  end
  ...
end

# 以下、テストコードから呼び出される共通メソッド
def can_see(text)
  # rspecにおけるassertion
  expect(elems_of_str(text)).not_to be_empty 
end

def elems_of_str(text)
  find_elements(:name, text) # ← これが使えなくなるのが痛い…。
end
{% endhighlight %}

テストコードの中で気軽に使っていた`can_see(text)`は、実際に数えてみると、72箇所で呼び出していた。これは、上のコードを見れば分かるように、Appium1.4が提供してくれているUI部品取得メソッド`find_elements(:name, text)`に依存している。

Appium 1.5に移行しようとして困るのは、この「`:name`を指定したUI部品(Element)の取得」（Name locator strategy）ができなくなってしまうことなのだ。合計72箇所のコードに対して手を打たなければならないかと思うと、クラクラしてきた。

# 3. みんな、どうしてる？

この問題については、他の人もきっと困っているだろうと思って、ググってみた。案の定、Appium本家のフォーラムでも、issueとしてこの話題のスレッドが立っていた。

[Appium 1.5 fails to find_element_by_name](https://discuss.appium.io/t/appium-1-5-fails-to-find-element-by-name/8857)

スレッドの流れとしては、こんな感じ。(かなり意訳御免)

>「nameでElementを取得しようとしたけどエラーになった。」

>「Appium 1.5からはそういう仕様になったんだよ」

>「そうなんだ…。これまでのテストコードを直さなきゃ」

>「でも、どうやってテストしたいUI部品を指定しよう？ 今の仕様だと、XPathで対象Elementを指定するしかないのかな。」

> 「うわー、XPathは避けたい！」

> 「いや、まったく」

僕も、今Name locator strategyを使っている72箇所について、Elementを指定するためのXPathを調べて、テストコードに埋め込んでいくなんて猛烈にイヤだ…。

# 4. 突然降ってきた解決策

上記のスレッドは、今年の3月2日から一週間ほど議論が進み、「困った困った」で途切れていたんだけど、その4ヶ月後(今から1ヶ月ほど前)に、1つだけ新規コメントが付いて…

> Hi,
>
> For me (in ruby), 
> find_element(:name, "Button_Name") 
> becomes 
> find_element(:accessibility_id, "Button_Name")

ΣΣ(ﾟдﾟlll) マジですか！

試してみる。

{% highlight diff %}
diff --git a/appium_1.5/spec_helper.rb b/appium_1.5/spec_helper.rb
index 26bf5f6..46a7db4 100644
--- a/appium_1.5/spec_helper.rb
+++ b/appium_1.5/spec_helper.rb
@@ -45,7 +46,7 @@ def click_element_of(class_name, name: nil)
 end
 
 def elems_of_str(text)
-  find_elements(:name, text)
+  find_elements(:accessibility_id, text)
 end
 
{% endhighlight %}

Appium 1.5.3でE2Eテストが全件通りました。ヽ(´ー`)ノ

[![Appium1.5でテストが通った！][1]][1]

  [1]: /images/appium_tests_passed_on_Appium1.5.3.png

うまくいくようになった理由は、これから勉強します。( ˘ω˘)

# 5. ただ、もう一つ問題が…

ただ、Appium 1.5では、iOSシミュレータの問題が発生しました。続きは次の記事に書きます。

→ [【Appium 1.4→1.5移行】iOSシミュレータが2回起動する問題](/2016/09/03/Migration-to-Appium1.5-part2/)

