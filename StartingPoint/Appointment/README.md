# Appointment

## メモ

### gobuster

```bash
gobuster dir -w <passwordlist path> -u <url>
```

- 今回の問題では関係なかった
- 似たツールにdirb,Dirbusterがある

### MySQL Injection

- \#を使ってコメントアウトする

```sql
where username = '<ユーザネームの変数>' and　password = '<パスワードの変数>'; 
```

があったときに、ユーザネームの変数に``admin'#``をいれることでそれ以降のsqlがコメントアウトされる

```sql
where username = 'admin'#' and password = '任意の文字列';
```