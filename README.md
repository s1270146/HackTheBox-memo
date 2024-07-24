# Hack The Box メモ

# 目次

- [Hack The Box メモ](#hack-the-box-メモ)
- [目次](#目次)
- [手順](#手順)
  - [nmapでポートスキャン](#nmapでポートスキャン)
- [テンプレート](#テンプレート)
  - [nmap](#nmap)
  - [telnet](#telnet)
  - [ftp](#ftp)
  - [redis](#redis)
  - [smb](#smb)
- [ポート番号](#ポート番号)
  - [ウェルノウン](#ウェルノウン)
  - [それ以外](#それ以外)

# 手順

## nmapでポートスキャン

空いているポートを確認する

テンプレート: [nmapでポートスキャン](#nmapでポートスキャン)

# テンプレート

## nmap

```bash
nmap -p- --min-rate 10000 <IP address>

# -sV: Probe open ports to determine service/version info
# -p <小さい方の数字>-<大きい方の数字> ←で範囲指定
```

## telnet

ポート番号 23

以下のような重要なアカウントで空白がパスワードになっていることがある
- root
- admin
- administrator

## ftp

- ポート番号 21
- 匿名(anonymous)でログインできることがある
- ログイン成功するとステータスコード230が返ってくる

## redis

- データベース
- ポート番号 6379

テンプレート

```bash

# install
sudo apt install redis

# ログイン
redis-cli -h <ip address>

# サーバの情報や統計を取得
info

# データベースを指定(デフォルトが0)
select 0

# DB内のキーの数を取得
dbsize

# すべてのキーを取得
keys *

# 指定したキーの値を取得
get <key>
```

## smb

- Windows標準の通信規約 ファイル共有やプリンタ共有
- ポート番号: 445

テンプレート

```bash
smbclient -L <ip address>

smbclient \\\\<ip address>\\<Shared Folder>
```

[日本語doc(非公式？)](https://www.samba.gr.jp/project/translation/3.6/htmldocs/manpages-3/smbclient.1.html)

# ポート番号

## ウェルノウン

|番号|プロトコル|
|-|-|
|21|FTP|
|23|telnet|
|445|SMB|

## それ以外

|番号|プロトコル|
|-|-|
|6379|Redis|
