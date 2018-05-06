---
layout: post
title: iTunes Matchのステップ2の途中で止まっていた問題の対処法
tags:
- Mac OS X
- iTunes
---

iTunes Matchを始めるにあたって、マッチングの途中で処理が止まってしまったので、対策を調べてみた。

# 1. iTunes Matchで困ったこと

iTunes Matchのマッチングが途中で止まる問題に悩まされていましたが、同じ問題に悩む人は少なくないようで、Appleサポートコミュニティに良い情報がありました。

<div class="sharelink" style="padding: 6px; border: 1px solid #aaaaaa;  margin: 0px 0px 50px;"><a href="https://discussionsjapan.apple.com/thread/10147682" title="iTunes match日本版を始めたところステップ2のマッチングで止まってしまった。 | Apple サポートコミュニティ" target="_blank"><img src="http://capture.heartrails.com/120x90/shadow?https://discussionsjapan.apple.com/thread/10147682" width="120" height="90" style="float: left;"></a><a href="https://discussionsjapan.apple.com/thread/10147682" title="iTunes match日本版を始めたところステップ2のマッチングで止まってしまった。 | Apple サポートコミュニティ" target="_blank">iTunes match日本版を始めたところステップ2のマッチングで止まってしまった。 | Apple サポートコミュニティ</a><a href="http://b.hatena.ne.jp/entry/https://discussionsjapan.apple.com/thread/10147682"><img src="http://b.hatena.ne.jp/entry/image/https://discussionsjapan.apple.com/thread/10147682" alt="はてなブックマーク - iTunes match日本版を始めたところステップ2のマッチングで止まってしまった。 | Apple サポートコミュニティ" title="はてなブックマーク - iTunes match日本版を始めたところステップ2のマッチングで止まってしまった。 | Apple サポートコミュニティ"></a><br style="clear: both;" /></div>

以下、その情報を元に私が解決に至った方法をご紹介します。


# 2. まずは、マッチングの進捗状況を確認！

iTunesの「スマートプレイリスト」を下図のように作成しました。このリストで、「まだiTunes Matchで未処理の楽曲はどれか？」が把握できます。

[![進捗状況確認用のスマートプレイリスト][1]][1]

  [1]: /images/MySmartPlayList_on_iTunesMatch.png

AppleサポートコミュニティではMac版のスクリーンショットが紹介されていましたが、私のWindows版 iTunesでは、このような設定でスマートプレイリスト「iTunes Matchの状況」を作成しました。<br>
(プレイリストの名前は、何でもいいと思います)

### MacのiTunesでは？

ちなみに、このプレイリストをMacで見てみると、少しばかり文言が変わってきます。
「マッチ」(Win)が「一致」(Mac)になったり、「複製」(Win)が「重複」(Mac)になったり…。

[![同じプレイリストを、Macで見た場合][2]][2]

  [2]: /images/iTunesSmartList_on_Mac.png


# 3. 時間がかかりそうな楽曲は、バックアップを取っていったん削除！

作成したスマートプレイリストの楽曲一覧を見てみると、iTunes Matchがこれからエンコードしてアップロードしようとしているのがどの曲なのかが分かります。<br>
その中には、「これのエンコードとアップロードに時間がかかるなら、後回しでいいや」と言う曲もあることでしょう。

### 私が後回しにしたもの

私の場合は、以下のような楽曲(?)は後回しでいい、と思いました。

1. 娘の参考書などに付いてきたCD (TOEICや英検のヒヤリング対策CDなど)
2. 落語CD (私が大量に持っている落語CDはほとんどマッチしてくれないようです。T_T)
3. ラジオドラマ系CD

そこで、これらの楽曲は一旦バックアップを取って、iTunesから削除しました。

### 楽曲を削除する時のTIPS

削除しようとしても「スムーズに削除できたりできなかったり」とイライラすることもありました。<br>
そんな場合は、削除時のダイアログボックスで「これらの曲は、iCloudからも削除されます」にチェックを入れるとスムーズに削除できました。

[![楽曲削除時の確認ダイアログ][3]][3]

  [3]: /images/DeleteConfirm_on_iTunes.png

### 削除した効果

削除した曲も、いずれ時間のあるときにiCloudにアップロードし直す予定なので、今はiCloudから削除しても(私個人的には）問題ありません。

この処理のおかげで、6500曲前後あった楽曲が4000曲弱に減りました。<br>
でも、減った曲数ほとんどは、上にも書きましたように、娘の英語のヒヤリング試験対策の音声データでしたので、私的には心の負担は全くありませんでした。(^^;)

# 4. 再度 iTunes Match にトライ！

再度iTunes Matchを開始します。
ステップ1は、ミュージック・ライブラリの規模に応じてかなりの時間がかりました。<br>
(放っておいたので、どれくらいかかったかよく分からないんです ^^;)

気がついたらステップ2を通り過ぎ、ステップ3の「アップロード」が始まっていました。

私の場合、アップロード対象は380曲程度でしたが、「今何曲目のどの曲をアップロードしているのか」は表示されますので、進捗が分かって安心です。


# 5. ステップ3でiTunesが落ちる場合は、他のデバイスからの登録を試す！

私の場合、理由はよく分からないのですが、特定のアルバムを追加でアップロードしようとすると、iTunes自体が落ちてしまうことがありました。

そんな時には、Macの方にファイルを移して Macで「iTunes Matchのアップデート」を行うことで、アップロードに成功しました。


# 6. おわりに

以上の手順によって、私は無事にiTunes Matchを開始できました。

何らかのご参考になるようでしたら幸いです。