# wordpress

## 前提
Apache2のインストール
```
$ sudo apt install apache2
```
MySQLのインストール
```
$ sudo apt install mysql-server
```
インストールが正常に終了するとMySQLは自動で実行されている。MySQLが実行されているかを確認
```
$ sudo netstat -tap | grep mysql
tcp 0 0 localhost:mysql *:* LISTEN  2556/mydqld
```
MySQLの起動
```
$ sudo systemctl restart mydql.service
```

## WrodPressのインストール
```
$ sudo apt install wordpress
```

### Apache2 Webサーバの設定
