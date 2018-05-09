---
layout: post
title: macOSで pyenv install でビルドに失敗したときの対処メモ 
tags:
- Python
- Mac OS X
---

macOS上で、pyenvを使ってanaconda3をインストールしようとしたら、ビルドエラーが出た。

何がどう悪いのかよく分からなかったけど、ググったらビンゴな記事が見つかった。忘れそうなので、備忘録として書き残す。

# 症状

## 環境
- macOS 10.2.3
- pyenvはhomebrewでインストール済み
- pythonはまだインストールしておらず、macOS Sierraに最初から入っていたsystem版のみの状態。

## 困ったことの詳細

pyenvでanaconda3をインストールしようとしたら、以下のエラーが出た。

```bash
$ pyenv install --verbose anaconda3-4.3.0
Downloading Anaconda3-4.3.0-MacOSX-x86_64.sh...
-> https://repo.continuum.io/archive/Anaconda3-4.3.0-MacOSX-x86_64.sh
error: failed to download Anaconda3-4.3.0-MacOSX-x86_64.sh

BUILD FAILED (OS X 10.12.3 using python-build.20170319062451.35057)

(中略)

Last 10 log lines:
/var/folders/w_/wvrf31wn4q9gtsw_0ynvh6r80000gn/T/python-build.20170318215106.22698 ~
curl: (56) SSLRead() return error -9806
```

# 対処法

ググってもなかなかいい情報が見つからなかったけれど、こちらの記事に行き着いた。

[pyenv で管理しているpythonのバージョンを更新する - Qiita](http://qiita.com/toohsk/items/69eaf2ff9283ad1d1345)

この記事の後半に、

> 新しいpythonのバージョンをインストールしたら怒られた

という追記があり、同じ症状で困っていたことが記載されていた。対処方としては、以下のコマンドでGitを再インストールしたら、エラーが出なくなったそうな。

```bash
$ brew reinstall git --with-brewed-curl --with-brewed-openssl
```

私の場合も、これでOK。Anacondaがhomebrewでエラー無くインストールできました。 ＼(^o^)／
