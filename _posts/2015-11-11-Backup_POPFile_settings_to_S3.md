---
layout: post
title: Raspberry PI上のファイルをAmazon S3にバックアップする
tags:
- Raspberry PI
- AWS
---

- Raspberry PI2の上で、メールフィルタリング・アプリであるPOPFileを動かしている。
- でも、Raspberry PI2の信頼性は未知数（あまり期待していない）なので、設定情報や学習データなど、大事なファイルがいつ無くなってしまってもおかしくない状況。
- なので、Amazon S3に定期的にバックアップする仕組みを準備できないか、トライしてみた。

# AWS CLIのインストールと設定

## インストール

僕のRaspberry PI2では、一般的に用いられているRaspbianではなく、[ごく普通のUbuntu (14.04)をインストール](/2015/03/05/install_Ubuntu_to_SD/)していて、その上でPOPFileを動かしている。

まずはその環境からAmazon S3にアクセスできるようにするため、AWS CLIをインストールする。

{% highlight sh %}
$ sudo apt-get install awscli
{% endhighlight %}

数分ほど他のことをしている間に、インストールは無事終了した。

## AWS CLIの初期設定

次は、`aws configure`コマンドで、アクセスキー（ Access Key ）情報とリージョンを設定しておく。<br />
リージョンをちゃんと設定しておかないと、いざ`aws s3`コマンドを使おうとする際にエラーでこけるので、要注意！

{% highlight sh %}
$ aws configure
AWS Access Key ID [None]: <あらかじめ取得していたAccess Key ID>
AWS Secret Access Key [None]: <あらかじめ取得していた Secret Access Key>
Default region name [None]: us-east-1
Default output format [None]:
$
{% endhighlight %}

この設定で、既に作成してあったバケットを参照できるか試してみる。

{% highlight sh %}
$ aws s3 ls s3://
2013-01-14 16:41:00 aramah
2015-10-14 00:08:14 oyatsu
$
{% endhighlight %}

大丈夫だ、問題無い。

# POPfileバックアップ用のバケット作成

何はともあれ、ファイルを保存するためのS3バケット`popfile_back`を作成する。

{% highlight sh %}
$ aws s3 mb s3://popfile_back
make_bucket: s3://popfile_back/
$
{% endhighlight %}

## 比較的安価なS3 STANDARD-IAを使う

今年の9月に、[Amazon S3](https://aws.amazon.com/jp/s3/storage-classes/)で低頻度アクセス向けのストレージクラス「Standard - IA」が利用できるようになった。<br>
週一回POPFileのデータをバックアップする用途にはそれで十分なので、こちら↓のQiita記事を参考に、バケットの設定をしてみた。

[[aws-cli]Amazon S3のバケットに、ストレージクラス「Standard – IA」を適応する](http://qiita.com/takachan/items/a47dac5c98e1f3bac350)

まずは、バケットのポリシー設定コマンドに入力するためのJSONファイル `set_s3_standard_ia_lifecyclepolicy_config.json`を用意する。

{% highlight json %}
{
    "Bucket": "popfile_back", 
    "LifecycleConfiguration": {
        "Rules": [
            {
                "ID": "popfile_back_STANDARD_IA", 
                "Prefix": "", 
                "Status": "Enabled", 
                "Transitions": [
                    {
                        "Days": 30, 
                        "StorageClass": "STANDARD_IA"
                    }
                ]
            }
        ]
    }
}
{% endhighlight %}

このJSONファイルを使って、bucketのポリシー設定を行う。

{% highlight sh %}
$ aws s3api put-bucket-lifecycle-configuration --cli-input-json file://set_s3_standard_ia_lifecyclepolicy_config.json
$
{% endhighlight %}

設定できたことを確認する

{% highlight sh %}
$ aws s3api get-bucket-lifecycle-configuration --bucket popfile_back
{% endhighlight %}

このコマンドで出力されたJSONは、次の通り。

{% highlight json %}
{
    "Rules": [
        {
            "Status": "Enabled",
            "Prefix": "",
            "Transitions": [
                {
                    "Days": 30,
                    "StorageClass": "STANDARD_IA"
                }
            ],
            "ID": "popfile_back_STANDARD_IA"
        }
    ]
}
{% endhighlight %}

ちゃんと設定できていることを確認できた。


# バックアップ用スクリプトの作成

POPFileでバックアップを取っておきたいのは…

[![/var/lib/popfileの中身][1]][1]

  [1]: /images/popfile_files_to_backup.png

`/var/lib/popfile/`にある、設定ファイル`popfile.cfg`と、学習データ`popfile.db`の二つ。なので、次のようなシェルスクリプト`~/crons/popfile-backup-s3`を用意した。

{% highlight sh %}
#!/bin/sh

export tgz_path=/tmp/popfile_back.tgz

echo [Backup POPFile data to S3] `date`
tar zcvf $tgz_path /var/lib/popfile/popfile.db /var/lib/popfile/popfile.cfg
/usr/bin/aws s3 cp $tgz_path s3://popfile_back
{% endhighlight %}

そして、このシェルスクリプトを呼び出すcron用のスクリプトを、`/etc/cron.weekly/`
フォルダに作成した。内容は次の通りで、動作確認ログは`/tmp/backup_to_s3.log`に出力する。（←手抜き）

{% highlight sh %}
#!/bin/bash

/home/ubuntu/crons/popfile-backup-s3 >> /tmp/backup_to_s3.log 2>&1
{% endhighlight %}

これで、週に一回S3にデータがバックアップされる…はず。（まだ1週間経っていないので、検証できてない。^^;）

このバックアップ先のバケットは、AWSコンソールからバージョン管理機能を有効にしておいたので、そのうち「上書きしてしまったtgzファイルの以前のバージョン」を取り出すのも試してみたいけど、今日はここまで。( ˘ω˘)