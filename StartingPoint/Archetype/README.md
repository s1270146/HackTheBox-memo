# Archetype

## メモ

- ``nmap -sC -sV <IP>``じゃないと表示されないポートがあるのはなぜ？
  - その前に実行していたのが``nmap -p- --min-rate 5000 <IP>``だったから
- Microsoft のsqlサーバにログインする 
  - [1433 - Pentesting MSSQL - Microsoft SQL Server](https://book.hacktricks.xyz/network-services-pentesting/pentesting-mssql-microsoft-sql-server)
  - Admin Password
    - MEGACORP_4dm1n!!

## 手順

- ``nmap -sC -sV 10.129.184.28``でポートスキャン
- ``smbclient -L 10.129.184.28``でSMBのフォルダを確認
- ``smbclient \\\\10.129.184.28\\backups``で``prod.dtsConfig``をダウンロード
  - 設定ファイルっぽい
- [HackTricks Pentesting MSQL](https://book.hacktricks.xyz/v/jp/network-services-pentesting/pentesting-mssql-microsoft-sql-server)
  - Manual Enumeration
  - ``mssqlclient.py``
- Windows local privilege escalation
  - ``WinPEAS``
- impacket
  - ``python mssqlclient.py ARCHETYPE/sql_svc@10.129.95.187 -windows-auth``

## チートシート

```sql
EXEC sp_configure 'show advanced options', 1;
RECONFIGURE;
sp_configure; -- Enabling the sp_configure as stated in the above error message
EXEC sp_configure 'xp_cmdshell', 1;
RECONFIGURE;
```

### ChatGPT解説
以下のSQL文は、Microsoft SQL Serverでのシステム構成オプションを変更および有効化するためのものです。それぞれの行について解説します。

### 1. `EXEC sp_configure 'show advanced options', 1;`
- **意味**: `sp_configure` ストアドプロシージャを使用して、SQL Serverの「高度なオプション」を表示する設定を有効にします。
- **解説**: SQL Serverには通常表示されない「高度なオプション」が存在します。この設定を1（true）にすると、それらのオプションが表示され、変更可能になります。

### 2. `RECONFIGURE;`
- **意味**: 変更を適用します。
- **解説**: `sp_configure` で設定を変更した後、 `RECONFIGURE` ステートメントを実行することで、その変更をSQL Serverに反映させます。

### 3. `sp_configure;`
- **意味**: 現在の構成設定を表示します。
- **解説**: `sp_configure` を引数なしで実行すると、現在のすべての構成オプションのリストとその設定値が表示されます。

### 4. `EXEC sp_configure 'xp_cmdshell', 1;`
- **意味**: `xp_cmdshell` 機能を有効にします。
- **解説**: `xp_cmdshell` は、SQL Serverからコマンドラインを実行するための機能です。これを1（true）に設定することで有効化されますが、セキュリティ上のリスクがあるため、慎重に使用する必要があります。

### 5. `RECONFIGURE;`
- **意味**: 変更を適用します。
- **解説**: 上記で行った `xp_cmdshell` の設定変更を適用します。

### 全体の流れ
1. まず、「高度なオプション」を表示するように設定を変更します。
2. その変更を適用し、確認します。
3. 次に、`xp_cmdshell` 機能を有効にし、その変更を適用します。

これにより、SQL Serverからコマンドラインコマンドを実行できるようになりますが、`xp_cmdshell` の使用はセキュリティリスクが伴うため、使用時には十分に注意が必要です。
