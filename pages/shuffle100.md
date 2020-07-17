---
layout: page
title: iOSアプリ「百首読み上げ」
tags: 百人一首, RubyMotion
sidebar_link: true
---

人の代わりに百人一首の読み手を務めてくれるiOSアプリを作りました。
iPhone, iPad, iPod Touchで動きます。

[English descriptions of this App are attached at the end.]

![読み上げ画面のイメージ](/images/Shuffle100_600.png)

ボーカロイド音声(※1)で、百人一首の歌をランダムに読み上げます。

こんな感じで使います。(競技かるた用の通常モードでの使用例です。)

<iframe src="//player.vimeo.com/video/88511077" width="640" height="360" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>



# 設定できること

### 1. 通常モード/初心者モード

上の句から読み始めるのか(初心者モード)、前の歌の下の句から読み始めるのか(通常モード)を選べます。

| モード | 試合の流れ |
| ------ | ---------- |
| 通常モード(競技かるたモード) | (試合再開) → 下の句読み上げ → 次の歌の上の句読み上げ → (試合一次停止) |
| 初心者モード(散らし取りモード) | (試合再開) → 上の句読み上げ → 下の句読み上げ → (試合一次停止) |

初心者モードでは、こんな流れになります。
<iframe src="//player.vimeo.com/video/104796183" width="640" height="360" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>


### 2. 試合に使う歌の選択

100首の中から、試合に使う歌をお好みで選べます。

### 3. 空札(からふだ)の有無

読み上げる札の中に、空札(取り札が無い「ハズレ」札)を入れるかどうかを設定できます。
空札は、「試合に使う歌」と同数の札がランダムに選ばれます。

### 4. 歌と歌の間の間隔

前の歌を読み終わってから次の歌を読み上げるまでの間隔を設定できます。

### 5. 上の句と下の句の間の間隔

上の句を読み終わってから下の句を読み上げるまでの間隔を設定できます。
(初心者モードでのみ設定できます。)


# ダウンロード

↓こちらをクリック！

<a href="https://itunes.apple.com/jp/app/%E7%99%BE%E9%A6%96%E8%AA%AD%E3%81%BF%E4%B8%8A%E3%81%92-hyaku-doku-or-shuffle-100/id857819404?mt=8" style="display:inline-block;overflow:hidden;background:url(//linkmaker.itunes.apple.com/assets/shared/badges/ja-jp/appstore-lrg.svg) no-repeat;width:135px;height:40px;background-size:contain;"></a>

# 感想・要望など

こちらのTwitterアカウントまで。→ [@Shuffle100](https://twitter.com/shuffle100)

# プライバシーポリシー

[プライバシーポリシーのページ][pvivacy_policy]

 [pvivacy_policy]: /pages/privacy_policy/


# ソースコード

このアプリのソースは、[GitHub](https://github.com/satoyos/Shuffle100_Swift)で公開しています。
また、ボーカロイド音声の歌唱データも[こちらのページ](https://github.com/satoyos/HyakuninIsshuVocaloidScore)で公開しています。

---
(※1) ボーカロイドはヤマハ株式会社の登録商標です。

---
---
 [Below are English descriptions of this App]

Kyogi-Karuta is a Japanese traditional game using the "Hyaku-nin-Isshu"(*1) Karuta cards.

(*1 .. It means "Single Verses by a Hundred People", selected by Teika Fujiwara about 800 years ago, as "The All Time Best 100 in these 600 years" at that time.)

This App supports playing the game, by singing verses at random, with VOCALOID voice.

You can watch Demo video;

<iframe src="//player.vimeo.com/video/88511077" width="640" height="360" frameborder="0" webkitallowfullscreen mozallowfullscreen allowfullscreen></iframe>


# Settings

### 1. Timing of pause

 Mode | Description |
| ---- | ----------- |
| Normal mode | Mode for Kyogi-Karuta. VOCALOID pauses singing just after 1st half of a verse. |
|Beginner mode | Recommended mode for Beginner. VOCALOID pauses after singing a whole verse.|

### 2. Verses to sing

You can select verses to use as you like.

### 3.  Use "Kara Fuda"(*2), or not. (*2 .. It means "Fake Verses")

Fake Verses can be added in Normal mode. Additional fake verses are collected at random, as many as "Verses to sing" you selected in 2).

### 4.  Interval time between verses

(by second)

### 5.  Interval time between 1st half of a verse, and 2nd half of it

(by second, in Beginner mode only)

# Support Account (Twitter)

HERE! => [@Shuffle100](https://twitter.com/shuffle100)

# Privacy Policy

[Our Privacy Policy][pvivacy_policy]


# Source Code

I'm developing this App as a Open Source Project in [GitHub](https://github.com/satoyos/Shuffle100_Swift).
You can VOCALOID score data in [this page](https://github.com/satoyos/HyakuninIsshuVocaloidScore).
Commets and Pull Requests are welcome !!

# And..

I apologize that this App supports Japanese only. If many people hope English support, I'll give it a try!

------------------------------------------------------------------------------------
VOCALOID is a registered trademark of Yamaha Corporation.
