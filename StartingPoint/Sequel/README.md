# Sequel

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

mysql内でバージョンを確認

```sql
select version();
```