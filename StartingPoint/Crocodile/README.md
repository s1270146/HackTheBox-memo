# Crododile

## 手順

- nmap
  - ftp 21
  - webサーバ 80
- ftpでanonymousログイン
- ユーザリストとパスワードリストを取得
- webサーバにログインページを探すためにブルートフォース

ログインページさがし
```bash
gobuster dir -w /usr/share/seclists/Discovery/Web-Content/Logins.fuzz.txt -u <hostname> -x php
```

-xは拡張子指定