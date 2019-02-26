+++
date = "2019-02-25T12:35:00+09:00"
title = "hugo+netlifyでブログ公開"
categories = ["memo"]
tags = ["github","hugo","netlify"]
image = "/image/190225_head.jpg"
+++

日頃ポートフォリオサイトの制作になかなか時間がとれず、何度かwordpressで作ろうと試みたものの、ことごとく途中で投げ出してきました。

<!--more-->

作品集のような私的なものは静的htmlで地道に作った方が早いという結論に至ったのですが、

* 更新スピードが遅く、ローカルに保存したサイトデータをそのうち紛失してしまう
* 上記と同じ理由でCSS、Jsなどの構造を忘れる
* 年に数回しか更新しないサイトのためにレンタルサーバーを借りたくない

といった問題が出てきました。

そこで、気軽に更新ができ、構築/運用が楽な静的サイトジェネレーターHugoを使ってみました。

サイトジェネレーターによる運用は、管理の手間から脱却でき、スピードも速く、blogの引っ越しなども比較的敷居が低い印象があります。

何より、更新を管理画面なしで完結させられるメリットは大きく、デザインの修正はGitHub上でブランチを切ってテストすることもできます。

## 使ったもの

1. GitHub
+ Go環境
+ Netlifyアカウント
+ Windows 10

## Goのインストール

[こちら](https://qiita.com/yoskeoka/items/0dcc62a07bf5eb48dc4b)を参考にインストールしました。

        $ go version
        go version go1.10.2 windows/amd64

のように表示されれば完了です。

## Hugoでブログを作る

プロジェクト作成

        $ hugo new site blog

プロジェクト直下に移動

        $ cd blog

リポジトリ作成

        $ git init

テーマをサブモジュールとして追加

        $ git submodule add https://github.com/taikii/whiteplain themes

cloneでも可だが、**netlify公開の場合はsubmoduleとして追加**します。
テーマは[whiteplain](https://github.com/taikii/whiteplain "_blank")を選択しました。

とりあえず確認のため、プロジェクト直下にexampleSiteをコピー

        $ cp -r  themes/whiteplain/exampleSite/* .

ローカルサーバー立ち上げ

        $ hugo server -D

[localhost:1313](http://localhost:1313/)へアクセス

テーマと同じサイトができていれば成功。

あとはgithubへコミット。

## Netlifyで公開

[こちら](https://app.netlify.com/start)のページからContinuous Deployment > 'GitHub'をクリック

![alternate text](/image/190225_00.jpg)

ポップアップウィンドウが開き、GitHubをオーサライズ後に以下の画面になります。

先ほどコミットしたリポジトリをクリック。

![alternate text](/image/190225_01.jpg)

Deploy settingの画面になるので、hugoのバージョンをローカルと合わせて'Deploy site'をクリック。

![alternate text](/image/190225_02.jpg)

僕の場合、ローカルのバージョン(0.55)ではデプロイが止まってしまい、デフォルト値(0.42)に戻すと正常に完了しました。
