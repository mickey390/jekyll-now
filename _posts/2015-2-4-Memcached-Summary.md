---
layout: post
title: Memcachedに関してのまとめ
---

## ◎公式

<http://memcached.org/>  
<https://github.com/memcached/memcached>

## ◎アーキテクチャ

参考 <http://gihyo.jp/dev/feature/01/memcached>

理解しておくワード

- libevent
- Slab Allocation
 - フラグメンテーション
 - Page/Chunk/Slab Class
 - メモリの無駄領域が発生
 - Growth Factor
- データ削除
 - Lazy Expiration
 - LRU

- 分散
 - 複数台使うときは、クライアントで配置ロジックを考える。台数の増減も考慮すること。
 - Consistent Hashing


## ◎ツール

### phpmemcacheadmin

mysqladminのように、GUIでmemcachedの操作を行ったり、
監視しやすくする。
<https://code.google.com/p/phpmemcacheadmin/>

### memcached-tool

statやslabの情報を見やすくするスクリプト  
<http://taka512.hatenablog.com/entry/20110830/1314698515>


## ◎監視項目

### statsコマンド

- evictions
 - 期限内のデータが削除された件数。
 - いつ、どこで発生したか分かりにくいので、履歴を取ったり、phpmemcacheadminを使って調べる。

- curr_connections
 - 現在のコネクション数
 - 履歴をとったほうがよい。アクセスなどの利用状況にそって増減しているかを確認する。


## ◎起動オプション

（編集中）
