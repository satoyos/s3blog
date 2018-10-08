---
layout: post
title: 【iOS】外部ライブラリの読み込みに失敗する際の対処メモ
tags:
- Xcode
---

iOSアプリの開発時に、外部ライブラリをCocoapodsで導入したんだけど、ユニットテストは問題なく動くのに、なぜかUIテスト時に動かなかったときの覚書き。

# 起こったこと

UIテスト(XCUITest)を動かそうとしたら、以下のエラーログを出力してシミュレータ上でテストイメージが動かず、困ってしまった。

```
2018-10-08 18:43:20.753373+0900 (中略)
...UITests): Library not loaded: @rpath/RevealServer.framework/RevealServer
```

# 対処1: ターゲットへリンクを手動で設定する

ググってみたら、UIテストのターゲットの "Build Phases" の設定の中で、 "Link Binary With Libraries” にその外部ライブラリを追加すべし、とのこと。

なので、やってみた。

![フレームワークをLink Binary設定に追加](/images/AddRevealFWtoXcode_lined.png)

でも、やはり同じログを出力して失敗する。

# 対処2: 該当フレームワークをOptional設定にする。

もっと調べてみると、「追加したフレームワークは、StatusがRequiredのままだと組み込みに失敗するが、Optionalだと成功した」という情報も見つかった。

そちらを試してみる。

![フレームワーク設定をOptionalに変更](/images/SetFWasOptional.png)

これでうまくUIテストが動いてくれた。

# おわりに

どういう仕組みで失敗し、対処によってうまくいったのかが分からないんだけど。。。

1. 動かないテストのターゲットで、Link Binary With Libraliesに該当ライブラリを追加する。
2. そのライブラリのStatusをRequiredからOptionalに変更する。

という2段階の対処によって、今回の問題は解決した。すぐに忘れてしまいそうなので、美貌のために書き残す。

