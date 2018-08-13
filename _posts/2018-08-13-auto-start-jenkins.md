---
layout: post
title: macOS上でJenkinsを自動起動させる
tags:
- Jenkins
- Mac OS X
---

iOSアプリ[「百首読み上げ」](/pages/shuffle100/)のUIテストにはAppiumを使っているが、用意したすべてのテストを完走すると、1時間弱の時間がかかってしまう。

開発に使っているMacBookがその間テストで塞がってしまうのはもったいないので、2012年もののMac miniでJenkinsを走らせている。

Jenkinsでは、

- GitHubのリポジトリにpushが行われたことを検知して、
- アプリのビルド、ユニットテスト、UIテスト(Appium)を一通り行い、
- 結果をSlackで通知

までを自動化してくれる。

このJenkinsは今までいちいち手動で起動しているのだが、調べてみると、簡単にサービスとして自動起動できることがわかったので、その手順を書き残す。

# 自動起動設定はコマンド一発

HomebrewでインストールしたJenkinsは、下記コマンド一発でサービス起動の設定ができる。

```bash
$ brew services start jenkins
```

実に簡単。

# 他のマシンからのアクセスに対応させる

ただし、一点だけ問題があった。

Jenkinsが動作するMac miniはディスプレイを繋いでいないので、これまではMacBookからリモートでJenkinsのコンソール画面にアクセスしていた。しかし、上記のコマンドで自動起動させた場合、デフォルトではローカルマシン(127.0.0.1)でしかアクセスできないようなのだ。

## 設定ファイルを書き換える

HomebrewでJenkinsをサービス起動させると、設定ファイルとしては`~/Library/LaunchAgents/homebrew.mxcl.jenkins.plist`を使う。

このファイルは、`/usr/local/Cellar/jenkins/[バージョン番号]`からコピーしてくるようなので、そのコピー元ファイルのバックアップを取った後に、内容を書き換えた。

具体的には、

```xml
      <string>--httpListenAddress=127.0.0.1</string>
```

となっている行を、次のように書き換える。

```xml
      <string>--httpListenAddress=0.0.0.0</string>
```

`httpListenAddress`を`0.0.0.0`に書き換えることで、どのマシンからのアクセスも受け付けるようになる。(その分、セキュリティ上のリスクを負うわけだけど)

ちなみに、Jenkinsの起動時のパラメータについては、下記のJenkins Wikiページ(日本語)が参考になる。

[Starting and Accessing Jenkins](https://wiki.jenkins.io/display/JA/Starting+and+Accessing+Jenkins)

# おわりに

これでJenkins自体は自動起動できるようになった。あとは、JenkinsでUIテストを行う際に利用するAppiumも自動起動させられれば完璧なんだけど…

今はカンタンにAppium環境を準備できるパッケージ[Appium Desktop](https://github.com/appium/appium-desktop)を使っていて、これはGUI操作を前提としているため、サービス自動起動には対応していないみたいだ。

あと一歩、というところである。(-_-;)