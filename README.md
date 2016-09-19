# wordpress
Ubuntuにwordpressをインストールする。

## 前提
LAMP(Linux+Apache+MySQL+PHP)の環境
taskselのインストール
```
$ sudo apt install tasksel
```
taskselの起動
```
$ sudo tasksel
```
LAMP serverにチェックして<了解>をクリック。MySQLの"root"ユーザに対する新しいパスワードの入力を求められる。もう一度、確認のためにパスワードの入力を求められる。

Apache2のインストール。taskselでLAMP serverをインストールした場合は不要。
```
$ sudo apt install apache2
```
MySQLのインストール。taskselでLAMP serverをインストールした場合は不要。
```
$ sudo apt install mysql-server
```
インストールが正常に終了するとMySQLは自動で実行されている。MySQLが実行されているかを確認
```
$ sudo netstat -tap | grep mysql
tcp 0 0 localhost:mysql *:* LISTEN  23962/mydqld
```
MySQLの起動
```
$ sudo systemctl restart mydql.service
```

## WordPressのインストールと設定
```
$ sudo apt install wordpress-l10n
```
/usr/share/doc/wordpress/を読む。
```
$ cd /usr/share/doc/wordpress/
$ sudo gzip -d setup-mysql.gz
$ sudo bash setup-mysql -n wordpress example.jp
```
### Apache2 Webサーバの設定
