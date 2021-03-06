+++
date = "2019-02-26T12:35:00+09:00"
title = "hugoで構築したサイトへFancybox実装"
categories = ["memo"]
tags = ["github","hugo","netlify","jQuery"]
image = "/contents/images/190226_head.jpg"
+++

ポートフォリオサイトの制作に合わせて、多くの画像ファイルを並べる必要が出てきたため、hugoのshortcodeを活用して、Fancyboxを実装しました。

<!--more-->

## 準備するもの

1. 作品画像
+ 作品画像サムネイル
+ shortcode htmlファイル

## 記事.md

記事ファイル(*****.md)の、ギャラリー化させたい箇所へ以下を記入。

        {{　< gallery dir="画像格納ディレクトリ" >　}}

## ヘッダーテンプレート

> /themes/テーマ名/layouts/partials/header.html 等

        <script type="text/javascript" src="https://cdnjs.cloudflare.com/ajax/libs/jquery/3.3.1/jquery.min.js"></script>

既にjQueryがインポートされている場合は、必要ありません。

## shortcode

> /themes/テーマ名/layouts/shortcodes/gallery.html

        <!-- 初回のみJs読み込み //-->
        {{- if not ($.Page.Scratch.Get "gallery") }}
          <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/fancybox/3.4.0/jquery.fancybox.min.css" />
          <script src="https://cdnjs.cloudflare.com/ajax/libs/fancybox/3.4.0/jquery.fancybox.min.js"></script>
          <script>
            $(window).on('load resize', function(){
        			itemWidth = $('.item').width(); //サムネイル横幅取得
        			$('.item').height(itemWidth); //サムネイルを正方形にする
        			itemMargin = $('.item').outerWidth(true)-$('.item').height(); //margin取得
        			$('.item').css('margin-bottom', itemMargin*2+'px'); //margin-bottom定義
            });
          </script>
        {{ end }}
        {{- $.Page.Scratch.Add "gallery" 1 }}

        <div class="gallery">
        	<!-- 画像パス取得 //-->
        	{{ $path := print "/static" (.Get "dir") "/thumbs" }}
        	{{ $imgPath := print "" (.Get "dir") }}
        	{{ $thumbPath := print (.Get "dir") "/thumbs" }}
        		<!-- 画像枚数分繰り返し //-->
        		{{ range (readDir $path) }}
        		<!-- 画像パス //-->
        		{{ $imgsrcPath := print $imgPath "/" .Name }}
        		<!-- サムネイル画像パス //-->
        		{{ $thumbsrcPath := print $thumbPath "/" .Name }}
        		<div class="item" style="background-image:url({{$thumbsrcPath}});"><a data-fancybox="gallery" href="{{$imgsrcPath}}" title="{{ .Name }}"></a></div>
        	{{ end }}
        </div>

\<script\>\</script\>内は表示部分を調整するためのものなので、書かなくても表示されるはず。

## css

列数の指定も簡単にできそうですが、ひとまず今回は2列固定表示にしました。

          div.item {
            width:49.5%;
            float:left;
            display:block;
            background-size:cover;
            background-position:center top;
          }
          div.item:nth-child(odd){
            margin-right:.5%;
          }
          div.item:nth-child(even){
            margin-left:.5%;
          }
          div.item a {
            width:100%;
            height:100%;
            display:block;
          }

## 実行例

        {{ < gallery dir="/contents/images/190226" > }}

指定したディレクトリの中にthumbsディレクトリを作り、そこに同ファイル名のサムネイルを格納しています。

{{< 2colgallery dir="/contents/images/gallery" name="cat_" >}}
