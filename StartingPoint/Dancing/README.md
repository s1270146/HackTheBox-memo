# Dancing

## メモ

smb Windows標準の通信規約 ファイル共有やプリンタ共有
ポート番号 445
``smbclient -L <hostname>``

-Lでリストを表示

## 手順

1. ``smbclient -L <ip address>``
2. ``smbclient \\\\<ip address>\\<Shared Folder>``