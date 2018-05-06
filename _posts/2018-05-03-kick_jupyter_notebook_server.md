---
layout: post
title: macOSログイン時に Jupyter Notebookを自動起動させる
tags: Mac, Python
---

これまで、自宅サーバーとしてはRaspberry Piを使ってきたんだけど、
- やっぱり性能にちょっと不満がある
- iOSアプリ開発のCI(継続的インテグレーション)をやりたい

などと色々と思うところあって、Mac mini (Late 2012)を macOSサーバーとして立てた。
そして、他のPCやタブレットからいつでもJupyter Notebookが使えるよう、このサーバーでnotebookサーバーを自動起動するようにした。

本当は、ユーザログインしなくても自動起動するよう、サービス起動の形で設定したかったのだが、敷居がちょっと高そうなので、まずは「ユーザーとしてログイン」した時に自動起動することを目指した。

やるべきことは2つ。
1. Jupyter Notebookの起動シェルスクリプトをログイン時に実行
2. アクセス可能な機器のIPアドレスとトークンの設定

## 1. Jupyter Notebookの起動シェルスクリプトをログイン時に実行

まず、ログイン時に特定のアプリを自動起動するようにするための方法は、[Appleサポートページ](https://support.apple.com/kb/PH25590?locale=ja_JP)で紹介されているように、「システム環境設定」→「ユーザとグループ」→「ログイン項目」で指定しておけばいい。

そこで指定するシェルスクリプト自体はシンプルで、
 - Jupyter Notebookのホームディレクトリに移動し、
 - Jupyter Notebook起動する

だけやることにした。具体的には、次のようなスクリプトを`jupyter.command`として用意した。

```sh
#!/bin/sh
cd ~/src/jupyter && jupyter notebook
```

このファイルを「ログイン項目」に追加してやると、notebookサーバーが自動起動するようになった。

### 拡張子に注意

ここで、拡張子を`.command`にしたのは、先人の記事に従った結果なんだけど、「シェルスクリプトだったら`.sh`でもいいんじゃない？」と思って試しに`.sh`を指定したら、なんとXcodeが起動して、そのシェルスクリプトを開いてしまった！

やりたいのは「ターミナルを起動し、その上でシェルスクリプトを実行する」ことであって、そのためには拡張子は`.command`じゃないとダメらしい。勉強になった…。


## 2. アクセス可能な機器のIPアドレスとトークンの設定

設定ファイル`.jupyter/jupyter_notebook_config.py`を編集して
- 他の機器(PCやタブレット)からnotebookサーバーにアクセスできるようにした。
- アクセス時に必要になるトークンを設定した。

### アクセス可能な機器のIPアドレス

デフォルトの設定では、notebookサーバーを起動した機器(localhost)からしかできない。
そこで、設定ファイルの下記の部分を書き換えた。

```python
# (以下、抜粋)

# The IP address the notebook server will listen on.
c.NotebookApp.ip = '0.0.0.0' # <= 元々は '127.0.0.1'(localhost)

# (以上、抜粋)
```

### トークン

今のJupyter Notebookセキュリティが強化され、パスワードかトークンを設定し、それをnotebookサーバーへのログイン時に指定する必要があるらしい。

なので、下記のQiita記事を参考にさせてもらい、上記の「IPアドレス」指定と同じ設定ファイル`.jupyter/jupyter_notebook_config.py`に設定を追記して、トークンを与えた。

[Jupyter notebookのパスワード](https://qiita.com/SaitoTsutomu/items/aee41edf1a990cad5be6)

具体的には、下記の1行を末尾に追加した。

```python
# (以下、末尾に追加)
c.NotebookApp.token = '[トークン文字列hogehoge]'
```

notebookサーバーにログインする際には、ここで指定したトークン文字列を入力する。

## おわりに

以上の設定により、macOSにログインした際に、Jupyter Notebookのサーバーも自動で起動するようになった。

今後は、「ユーザーログインしなくても自動起動」するようにしていきたいが、まずはここまでの成果を(きっと忘れるので)書き残す。