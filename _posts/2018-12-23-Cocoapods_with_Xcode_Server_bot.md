---
layout: post
title: 【iOS】Xcode 10のBotで、Cocoapodsを使ったプロジェクトをCIする
tags:
- Xcode
---

iOSアプリの開発はMacBook Pro上で行っているけれど、空いているMac miniでXcode Server Builderを動かし、ローカルCIマシンに仕立てようとしている。

ただ、Cocoapodsを使っているXcodeプロジェクトで必要に応じて`pod install`を実行するよう設定する際に、ちょっとしたコツがあったので、書き残す。

#  環境

MacBook Pro(開発機)、 Mac mini(CI機)のどちらも、ソフトウェア的には同じ環境。

- macOS 10.14.1 (Mojave) ← 
- Xcode 10.1

# 最初にやってみたこと

この課題についても、ありがたいことに先人の書き残してくれたノウハウ(以下の記事)があったので、大いに参考にさせてもらった。

[Xcode 9のBotでpod installする方法（小ネタ）](https://qiita.com/da1ssk/items/4def67c09b5d15692dee)

まずはこの記事のとおりにBotのTriggerを設定してみた。自分の環境では、podの所在は`~/.rbenv/shims/pod`なので、Triggerの設定内容は次のようになった。

```sh
#!/bin/sh

cd "${XCS_PRIMARY_REPO_DIR}"   # リポジトリのディレクトリに移動

~/.rbenv/shims/pod install     # 自分の環境でのpodのパスを設定
```

上記の参考記事だとこの設定でうまくいくはずなのだが、私の環境ではテストに失敗した。

# エラーの原因を探ってみる

テストに失敗する原因は何なのか？を調べようと思ったのだが、XcodeのBotが出力するエラーメッセージの意味がよくわからない。

```
(以下抜粋)
Error: /Users/yoshi/Library/Caches/XCSBuilder/Bots/2090ead361271445dc9f96926f012e92/Source/Shuffle100/Pods/Target Support Files/Pods-Shuffle100/Pods-Shuffle100.debug.xcconfig: unable to open file (in target "Shuffle100" in project "Shuffle100") (in target 'Shuffle100')
```

`pod install`がうまく機能しなかったのかもしれない…と考えて、CI機で実際にビルドが行われているディレクトリに行き、`pod install`してみた。すると、Warningが出た。

```
    WARNING: CocoaPods requires your terminal to be using UTF-8 encoding.
    Consider adding the following to ~/.profile:

    export LANG=en_US.UTF-8
```

これか？これなのか？

試しに、
```
$ export LANG=en_US.UTF-8; pod install
```
を実行してみた。おお、Warningが消えて、pod installが正常に動いた！

# Triggerを修正

というわけで、BotのTriggerの設定スクリプトにも、環境変数`LANG`の設定を追加した。

```
#!/bin/sh
cd "${XCS_PRIMARY_REPO_DIR}"   # リポジトリのディレクトリに移動

export LANG=en_US.UTF-8        # ← この行を新規に追加！

~/.rbenv/shims/pod install
```

これで、Integrationが正常に行われるようになった。

# おわりに

結局は、参考記事の設定に、環境変数LANGの設定を追加すればうまくいった。

そして、エラーメッセージが良く分からないときには、実際に何が起きているのかを知るために、実際にCI機のWorkフォルダでコマンドを叩いてみるべし！と肝に銘じた。