# Redeemer

## 手順

1. ポートスキャン
2. Redisのポートが空いている
3. redis-cliを使ってログイン
   - ``sudo apt install redis``
   - ``redis-cli -h <ip address>``
4. ``info``でサーバの情報や統計を取得
5. ``select 0``でデータベース指定
6. ``dbsize``でキーの数を取得
7. ``keys *``ですべてのキーを取得
8. ``get flag``でキーの中身を取得