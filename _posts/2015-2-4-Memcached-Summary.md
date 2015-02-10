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

- CASの無効化
 - 「-C            Disable use of CAS」
 - 有効化すると、トランザクション風の挙動が出来る。
 - 無効化すると、1itemにつき 8byteメモリ容量を削減できる。

- tcp_backlog
 - 「-b            Set the backlog queue limit (default: 1024)」
 - net.core.somaxconnと依存関係にあるので、変更が効いていないようであればこれをチューニングする。  
 - 参考 : <http://d.hatena.ne.jp/tetsuyai/20111220/1324466655>

（編集中）
