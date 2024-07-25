# Hack The Box メモ

# 目次

- [Hack The Box メモ](#hack-the-box-メモ)
- [目次](#目次)
- [ジャンル](#ジャンル)
  - [スキャン](#スキャン)
  - [リモートログイン](#リモートログイン)
  - [ファイル共有](#ファイル共有)
  - [Web Directory Fuzzing](#web-directory-fuzzing)
  - [Database](#database)
- [ツール](#ツール)
  - [nmap](#nmap)
  - [telnet](#telnet)
  - [ftp](#ftp)
  - [redis-cli](#redis-cli)
  - [smbclient](#smbclient)
  - [gobuster](#gobuster)
    - [SecList](#seclist)
- [テンプレート](#テンプレート)
  - [SQL Injection](#sql-injection)
    - [MySQL](#mysql)
- [ポート番号](#ポート番号)
  - [ウェルノウン](#ウェルノウン)
  - [それ以外](#それ以外)

# ジャンル

## スキャン

- ポートスキャン
  - 空いているポートを確認する
  - [nmap](#nmap)

## リモートログイン

- telnet
  - ポート番号 23
  - 以下のような重要なアカウントで空白がパスワードになっていることがある
      - root
      - admin
      - administrator
  - [telnet](#telnet)

## ファイル共有

- ftp
  - ポート番号 21
  - 匿名(anonymous)でログインできることがある
  - ログイン成功するとステータスコード230が返ってくる
  - [ftp](#ftp)
- smb
  - Windows標準の通信規約 ファイル共有やプリンタ共有
  - ポート番号: 445
  - [smbclient](#smbclient)

## Web Directory Fuzzing

- gobuster
  - ウェブのディレクトリやファイルを見つけるためにブルートフォースアタック
  - [gobuster](#gobuster)

## Database

- SQL Injection
  - [SQL Injection テンプレート](#sql-injection)
- redis
  - データベース
  - ポート番号 6379
  - [redis-cli](#redis-cli)

# ツール

## nmap

```bash
nmap -p- --min-rate 10000 <IP address>

# -sV: Probe open ports to determine service/version info
# -p <小さい方の数字>-<大きい方の数字> ←で範囲指定
```

## telnet

```bash
telnet <hostname>
```

## ftp

```bash
ftp <ip adress>
```

## redis-cli

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

## smbclient

```bash
smbclient -L <ip address>

smbclient \\\\<ip address>\\<Shared Folder>
```

[日本語doc(非公式？)](https://www.samba.gr.jp/project/translation/3.6/htmldocs/manpages-3/smbclient.1.html)

## gobuster

```bash
gobuster dir -w <passwordlist path> -u <url>
```

### SecList

パスワードリスト

[SecLists Github](https://github.com/danielmiessler/SecLists)

```bash
sudo apt install seclists
```

インストール後``/usr/share/seclists``ディレクトリにパスワードのリストが保管される

# テンプレート

## SQL Injection

### MySQL

- \#を使ってコメントアウトする

# ポート番号

## ウェルノウン

|番号|プロトコル|
|-|-|
|21|FTP|
|23|telnet|
|80|HTTP|
|443|HTTPS|
|445|SMB|

## それ以外

|番号|プロトコル|
|-|-|
|6379|Redis|
