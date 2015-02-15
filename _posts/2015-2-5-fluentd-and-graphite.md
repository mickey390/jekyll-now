---
layout: post
title: fluentdとgraphiteでモニタリング環境を構築する
---

## ◎概要

fluentdとgraphiteでモニタリング環境を構築する手順です。  
ubuntu14で行いました。

### 構成

スクリプト(モニタリングしたいもの)  
↓  
fluentd in_exec（インプット）  
↓  
fluentd fluent-plugin-graphite（アウトプット）  
↓  
graphite (データの保存をグラフ化)

### 運用時に必要なこと

- スクリプトの作成
- グラフの調整

## ◎fluentd

## 1.1.事前設定

参考
<http://docs.fluentd.org/ja/articles/before-install>

### ファイルディスクリプタの変更

```
ubuntu@monitor1001:~$  ulimit -n
1024
ubuntu@monitor1001:~$
```

小さいので、変更。


```
ubuntu@monitor1001:~$ sudo diff /etc/security/limits.conf /etc/security/limits.conf.org
56,61d55
<
< root soft nofile 65536
< root hard nofile 65536
< * soft nofile 65536
< * hard nofile 65536
<
ubuntu@monitor1001:~$
```



### ネットワーク関連


```
ubuntu@monitor1001:~$ sudo diff /etc/sysctl.conf /etc/sysctl.conf.org
61,66d60
<
< net.ipv4.tcp_tw_recycle = 1
< net.ipv4.tcp_tw_reuse = 1
< net.ipv4.ip_local_port_range = 10240    65535
<
<
ubuntu@monitor1001:~$

```

再読み込み

```
 sudo sysctl -p /etc/sysctl.conf
```



### 1.2.インストール

参考
<http://docs.fluentd.org/ja/articles/install-by-deb>

下記でインストール

```
$ curl -L http://toolbelt.treasuredata.com/sh/install-ubuntu-precise.sh | sh
```

そして再起動する。

```
ubuntu@monitor1001:~$ sudo /etc/init.d/td-agent restart
 * Restarting td-agent td-agent                                                                                                                                                                                                               [ OK ]
ubuntu@monitor1001:~$ sudo /etc/init.d/td-agent status
 * ruby is running
ubuntu@monitor1001:~$

```


【試し】


```
ubuntu@monitor1001:/etc/td-agent/config.d$ cat test.conf
<source>
  type exec
  command sh /home/ubuntu/test.sh
  keys k1,k2
  tag_key k1
  time_format %Y-%m-%d %H:%M:%S
  run_interval 10s
</source>

<match memcache0001>
  type file
  path /home/ubuntu/testdir/test.log
  time_slice_format %Y%m%d
  time_slice_wait 10m
  time_format %Y%m%dT%H%M%S%z
  compress gzip
  utc
</match>
ubuntu@monitor1001:/etc/td-agent/config.d$
```


再起動

```
 sudo /etc/init.d/td-agent restart
```



====================================================

【 fluentd plugin インストール 】

sudo /usr/lib/fluent/ruby/bin/fluent-gem install fluent-plugin-graphite







# 2.graphite

## インストール

```
sudo apt-get update
sudo apt-get install graphite-web graphite-carbon
```

メッセージが出るので
No
を選択




## DjangoのDB設定


- DB周りコンポーネントをインストール

```
sudo apt-get install postgresql libpq-dev python-psycopg2
```


- テーブル設定


```
ubuntu@monitor1001:~$ sudo -u postgres psql
psql (9.3.4)
Type "help" for help.

postgres=# CREATE USER graphite WITH PASSWORD '**********';
CREATE ROLE
postgres=# CREATE DATABASE graphite WITH OWNER graphite;
CREATE DATABASE
postgres=# \q
could not save history to file "/var/lib/postgresql/.psql_history": No such file or directory
ubuntu@monitor1001:~$
```


- アプリケーション設定

sudo vi /etc/graphite/local_settings.py



```
ubuntu@monitor1001:~$ sudo cp /etc/graphite/local_settings.py /etc/graphite/local_settings.py.org
ubuntu@monitor1001:~$
ubuntu@monitor1001:~$ sudo vi /etc/graphite/local_settings.py
ubuntu@monitor1001:~$
ubuntu@monitor1001:~$ sudo diff /etc/graphite/local_settings.py /etc/graphite/local_settings.py.org
13c13
< SECRET_KEY = '*******'
---
> #SECRET_KEY = 'UNSAFE_DEFAULT'
24d23
< TIME_ZONE = 'Asia/Tokyo'
125c124
< USE_REMOTE_USER_AUTHENTICATION = True
---
> #USE_REMOTE_USER_AUTHENTICATION = True
152,161d150
< #DATABASES = {
< #    'default': {
< #        'NAME': '/var/lib/graphite/graphite.db',
< #        'ENGINE': 'django.db.backends.sqlite3',
< #        'USER': '',
< #        'PASSWORD': '',
< #        'HOST': '',
< #        'PORT': ''
< #    }
< #}
164,168c153,157
<         'NAME': 'graphite',
<         'ENGINE': 'django.db.backends.postgresql_psycopg2',
<         'USER': 'graphite',
<         'PASSWORD': '********',
<         'HOST': '127.0.0.1',
---
>         'NAME': '/var/lib/graphite/graphite.db',
>         'ENGINE': 'django.db.backends.sqlite3',
>         'USER': '',
>         'PASSWORD': '',
>         'HOST': '',
171a161
>
ubuntu@monitor1001:~$
```

- DBの同期

sudo graphite-manage syncdb




- Carbon の設定

```
ubuntu@monitor1001:~$ sudo cp /etc/default/graphite-carbon /etc/default/graphite-carbon.org
ubuntu@monitor1001:~$ sudo vi /etc/default/graphite-carbon
ubuntu@monitor1001:~$ sudo diff /etc/default/graphite-carbon /etc/default/graphite-carbon.org
2,3c2
< #CARBON_CACHE_ENABLED=false
< CARBON_CACHE_ENABLED=true
---
> CARBON_CACHE_ENABLED=false
```



```
ubuntu@monitor1001:~$ sudo cp /etc/carbon/carbon.conf /etc/carbon/carbon.conf.org
ubuntu@monitor1001:~$ sudo vi /etc/carbon/carbon.conf
ubuntu@monitor1001:~$ sudo diff /etc/carbon/carbon.conf /etc/carbon/carbon.conf.org
26,27c26
< #ENABLE_LOGROTATION = False
< ENABLE_LOGROTATION = True
---
> ENABLE_LOGROTATION = False
ubuntu@monitor1001:~$
```


- strage 設定

```
ubuntu@monitor1001:~$ sudo diff  /etc/carbon/storage-schemas.conf  /etc/carbon/storage-schemas.conf.org
17,21d16
<
< [test]
< pattern = ^test\.
< retentions = 10s:10m,1m:1h,10m:1d
<
ubuntu@monitor1001:~$
```


- data収集

```
sudo cp /usr/share/doc/graphite-carbon/examples/storage-aggregation.conf.example /etc/carbon/storage-aggregation.conf


sudo vi /etc/carbon/storage-aggregation.conf

sudo service carbon-cache start
```


- apache設定


```
sudo apt-get install apache2 libapache2-mod-wsgi

sudo a2dissite 000-default

sudo cp /usr/share/graphite-web/apache2-graphite.conf /etc/apache2/sites-available
```


- 確認

monitor1001

をブラウザでみる。


以上。


