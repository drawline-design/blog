+++
date = "2019-02-27T12:35:00+09:00"
title = "netlifyのLarge Mediaでサムネイル生成を簡略化"
categories = ["memo"]
tags = ["github","hugo","netlify"]
image = "/contents/images/190225_head.jpg"
+++

デザイン系のポートフォリオサイトとなると、どうしても画像リソースが多くなってくるため、netlifyのLarge Mediaを使用してみることにしました。

メディアファイルをGit LFSへ移行することで、GitHubのリポジトリ上限である容量1GB、アップロード制限100MBを、ある程度気にせずにサイト運用が可能になります。

<!--more-->

また、表示する画像のサイズが臨機応変に指定可能(月間2,500リサイズまで)になります。

## 手順

詳細な手順はこちらから。

[Large Media](https://www.netlify.com/docs/large-media/)

## shortcode

サムネイル画像と拡大画像を作る必要がなくなりました。

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
        {{ $baseURL := .Site.BaseURL }}

        <style>
        	a.item {width:49.5%;float:left;display:block;background-size:cover;background-position:center top;}
        	a.item:nth-child(odd){margin-right:.5%;}
        	a.item:nth-child(even){margin-left:.5%;}
        	a.item img {width:100%;height:100%;display:block;}
        </style>

        <div class="gallery">
        {{- with (.Get "dir") -}}
        	{{- $files := readDir (print "/static/" .) }}
        	{{- range $files -}}
        		{{- $nameStr := $.Get "name" | default "_" }}
        		{{- $isimg := .Name | findRE ($nameStr) }}
        		{{- if and $isimg }}
        			{{- $linkURL := print $baseURL ($.Get "dir") "/" .Name | absURL }}
        			<a class="item" data-fancybox="gallery{{ $.Get "name" }}" href="{{ $linkURL }}"><img src="{{ $linkURL }}?nf_resize=smartcrop&w=300&h=300" title="{{ .Name }}" /></a>
        		{{- end }}
        	{{- end }}
        {{- end }}
        </div>

## .gitattributes

プロジェクト直下に置く場合

        static/contents/images/*.gif filter=lfs diff=lfs merge=lfs -text
        static/contents/images/*.jpg filter=lfs diff=lfs merge=lfs -text
        static/contents/images/*.png filter=lfs diff=lfs merge=lfs -text
        static/contents/images/gallery/*.gif filter=lfs diff=lfs merge=lfs -text
        static/contents/images/gallery/*.jpg filter=lfs diff=lfs merge=lfs -text
        static/contents/images/gallery/*.png filter=lfs diff=lfs merge=lfs -text

## 記事.md

引数'name'がファイル名の頭にくるファイルを検索し、一覧表示します。

        {{ < gallery dir="/contents/images/gallery" name="cat_" > }}

トラック済みの画像データは、以下の方法でサイズ指定、トリミングが可能です。

[Image Transformation](https://www.netlify.com/docs/image-transformation/)
