# Archetype

## メモ

- ``nmap -sC -sV <IP>``じゃないと表示されないポートがあるのはなぜ？
  - その前に実行していたのが``nmap -p- --min-rate 5000 <IP>``だったから
- Microsoft のsqlサーバにログインする 
  - [1433 - Pentesting MSSQL - Microsoft SQL Server](https://book.hacktricks.xyz/network-services-pentesting/pentesting-mssql-microsoft-sql-server)

## 手順

- ``nmap -sC -sV 10.129.184.28``でポートスキャン
- ``smbclient -L 10.129.184.28``でSMBのフォルダを確認
- ``smbclient \\\\10.129.184.28\\backups``で``prod.dtsConfig``をダウンロード
- [HackTricks Pentesting MSQL](https://book.hacktricks.xyz/v/jp/network-services-pentesting/pentesting-mssql-microsoft-sql-server)
  - Manual Enumeration
  - ``mssqlclient.py``
- Windows local privilege escalation
  - ``WinPEAS``