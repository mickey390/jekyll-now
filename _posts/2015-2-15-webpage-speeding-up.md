---
layout: post
title: Webページの高速化まとめ
---

Webページの高速化に必要なツールなどをまとめておく。  
サイトにより、測定方法や対策を変える必要がある。

# 測定
- サイト
 - [WebpageTest](http://www.webpagetest.org/)
 - [GooglePageSpeed](https://developers.google.com/speed/pagespeed/insights/?hl=ja)
 - GoogleAnalytics
- API
 - [GooglePageSpeed](https://developers.google.com/speed/docs/insights/v2/getting-started) → jenkinsやnagiosなどに設定してもよいかも。
 
# 対策
- CSS
 - CSSスプライト使う
 - ファイル数へらす
 - 圧縮する(サイズ削減)

- html
 - [dns-prefetch](https://developer.mozilla.org/ja/docs/Controlling_DNS_prefetching)
 - [ApplicationCache](http://www.html5rocks.com/ja/tutorials/appcache/beginner/)

- 画像
 - [webp](http://html5experts.jp/jxck/2550/)
 - [Data URI scheme](http://ja.wikipedia.org/wiki/Data_URI_scheme)

- webサーバ
 - [PageSpeed Module](https://developers.google.com/speed/pagespeed/module)
 - apache(mod-pagespeed)
 - nginx(ngx_pagespeed)
 

# ブラウザ依存確認
- <http://caniuse.com/>
- <http://mobilehtml5.org/>
- <http://html5please.com/>

# 参考サイト
- [Webサイト高速化テクニック – シリーズ –](http://dev.classmethod.jp/series/web%E3%82%B5%E3%82%A4%E3%83%88%E9%AB%98%E9%80%9F%E5%8C%96%E3%83%86%E3%82%AF%E3%83%8B%E3%83%83%E3%82%AF/)
