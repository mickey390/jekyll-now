---
layout: post
title: yii2アプリをheroku上に作成する
---


yii2を試しで触ってみる用に、
herokuにアプリを作成した時のメモです。


# インストール

```
# yii2のインストール
$ composer global require "fxp/composer-asset-plugin:1.0.0"
$ composer create-project --prefer-dist yiisoft/yii2-app-basic basic
```


# heroku

```
#アプリ作成
heroku create
git push heroku master

#git登録
heroku git:remote -a <appname>

#webサーバの設定
% cat Procfile
web: vendor/bin/heroku-php-nginx web
```


#参考
http://www.yiiframework.com/doc-2.0/guide-start-installation.html
