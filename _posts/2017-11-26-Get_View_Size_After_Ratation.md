---
layout: post
title: 【iOS】画面回転後のViewのサイズを取得する
tags:
- Xcode
---

iOSアプリの画面作成時に、AutoLayoutを使って定義したViewのサイズ取得にまつわる覚書メモ。画面回転【後】のサイズ取得に意外と苦労した、という話。

# やりたかったこと

iOSアプリの画面を作成する際に、AutoLayoutでViewの位置やサイズを指定していると、デバイスの回転時など様々なイベントでViewのサイズが動的に変わる。

その際、「回転後」のViewのサイズを取得したかったのだけど、試してみると回転【前】のViewサイズを取得してしまい、思うように回転【後】のサイズが得られずに苦労した。

調べたり試行錯誤したりした末に、自分なりに一番マシな方法が分かったので、書き残しておく。

# 環境(バージョン)

|:---|:--|
| Mac本体| MacBook Pro (13-inch, Late 2016, Four Thunderbolt 3 Ports)
| macOS | 10.13.1 |
| iOS | 11.1 |
| Xcode | 9.1 |
| 言語 | Swift4 |

# 試した方法

デバイスの回転時にどのイベントをフックしてViewのサイズを確認するか？には色々な方法があるようだけど、僕は以下のQiita記事で紹介されている4方式を試してみた。

[iOSでの各回転検知方法とその結果](https://qiita.com/www51k/items/7be9abff8f6efea5748e)

1. NSNotificationを利用する
2. viewWillLayoutSubviews()内で取得する
3. viewDidLayoutSubviews()内で取得する
4. viewWillTransition()内で取得する

# 結果

4つの方式を試す前に、一番期待していたのは「3. viewDidLayoutSubviews()内で取得する」だった。subviewのレイアウトが終わった時点なら、回転後のviewサイズを正確に得られるだろう、と考えたのだ。

でも、結果的に正しく取得できたのは「1. NSNotificationを利用する」だけで、他の方法では全て「回転直前のViewサイズ」しか取得できなかった。

理由については良く分からない。Notificationを介すると、Viewサイズ取得のタイミングが若干遅延して、結果的に回転【後】のサイズが取得できるのかな？？などと考えてみたり。

## Notificationを利用する具体的な方法

ViewControllerの`viewDidLoad()`でNotificationを登録し、回転検出時に呼ばれるハンドラを用意しておく。

```swift
    @IBOutlet weak var directView: UIView!  // サイズを調べたいView。
    
    // viewDidLoadでNotificationを登録
    override func viewDidLoad() {
        super.viewDidLoad()
        NotificationCenter.default.addObserver(
            self,
            selector: #selector(onOrientationDidChange(notification:)),
            name: NSNotification.Name.UIDeviceOrientationDidChange,
            object: nil)
    }

    // 回転時にNotificationを介して呼ばれるハンドラ
    @objc fileprivate func onOrientationDidChange(notification: Notification) {
        // サイズを調べるなど、回転【後】の調査はここで。
        print("directView(byNotificattion) => \(directView.frame.size)")
    }

```

# おわりに

画面の回転後に、更新されるViewのサイズに応じて文字のフォントサイズを変更するなど、「回転後のViewサイズを取得したい」というニーズは多いかと思っていたんだけど、ググってもあまりまとまった情報に行き当たらなかった。

もしかしたら、「もっとシンプルな手法があるのを知らなかった」というだけなのかもしれないけど。。。とりあえず、今の時点で分かったことを書き残した。


