# wordpress
ubuntu 16.04.1 LTSにwordpressをインストールする。

## 前提
LAMP(Linux+Apache+MySQL+PHP)をインストールするためにtaskselをインストールする。
```
$ sudo apt install tasksel
```
taskselの起動とLAMP serverのインストール
```
$ sudo tasksel
```
rスペースキーでLAMP serverにチェックを入れて<了解>をクリック。MySQLの"root"ユーザに対する新しいパスワードの入力を求められる。もう一度、確認のためにパスワードの入力を求められる。

次にPHPのextensionsをインストールする。
```
$ sudo apt update
$ sudo apt install php-curl php-gd php-mbstring php-mcrypt php-xml php-xmlrpc
$ sudo apt install php5-gd libssh2-php
```
Apache2のインストール。taskselでLAMP serverをインストールした場合は不要。
```
$ sudo apt install apache2
```
vhostとrewrite
```
$ sudo a2enmode vhost_alias
$ sudo a2enmode rewrite
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
インストールされたWordPressのプログラムのシンボリックリンクをapache2のルートディレクトリの下に作成する。これによりhttp://localhost/blogでアクセスできるようになる。
```
$ sudo ln -s /usr/share/wordpress/ /var/www/html/blog
または
$ sudo rsync -avP /usr/share/wordpress/ /var/www/html/blog/
```
wp-contentのパーミッションを変更する。
```
$ cd /var/www/html/blog
$ sudo chown -R <ユーザアカウント>:www-data *
$ mkdir /var/www/html/blog/wp-content/uploads
$ mkdir /var/www/html/blog/wp-content/grade
$ sudo chown -R :www-data /var/www/html/blog/wp-content/uploads
$ sudo chmod g+w /var/www/html/blog/wp-content
$ sudo chmod -R g+w /var/www/html/blog/wp-content/languages
$ sudo chmod -R g+w /var/www/html/blog/wp-content/plugins
$ sudo chmod -R g+w /var/www/html/blog/wp-content/themes
$ sudo chmod -R g+w /var/www/html/blog/wp-content/uploads
$ sudo chmod -R g+w /var/www/html/blog/wp-content/grade
```
または
```
$ sudo chown -R www-data:www-data /usr/share/wordpress/wp-content/
上の行は不要？
$ sudo chown -R www-data:www-data /var/www/html/blog
$ sudo chmod 755 /var/www/html/blog
または
$ sudo chmod g+w /var/www/html/blog/wp-content
$ sudo chmod -R g+w /var/www/html/blog/wp-content/themes
$ sudo chmod -R g+w /var/www/html/blog/wp-content/plugins
```
### MySQLにwordpress用のデータベースを作成
/usr/share/doc/wordpress/README.Debianの内容に従いMySQLの設定をする。下の例だとMySQLのユーザ名とテーブル名をwordpressとしている。またhttp://localhostでアクセスできるように設定する。/etc/wordpress/config-localhost.phpが作成される。
```
$ sudo gzip -d /usr/share/doc/wordpress/examples/setup-mysql.gz
<ローカルからアクセスする場合>
$ sudo bash /usr/share/doc/wordpress/examples/setup-mysql -n wordpress localhost
<インターネットからアクセスする場合>
$ sudo bash /usr/share/doc/wordpress/examples/setup-mysql -n wordpress example.com
```
データベースの確認
```
$ mysql -u root -p

mysql> show DATABASE;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| wordpress          |
+--------------------+
<データベースを削除するときは>
mysql> DROP DATABASE wordpress;
Query OK, 0 row affected (0.03 sec)
mysql> quit
Bye
```
## WordPressの設定
/etc/wordpress/config-localhost.phpを編集する。
```
$ sudo vim /etc/wordpress/config-localhost.php

define('WP_CONTENT_DIR', '/var/www/html/blog/wp-content'); # /var/lib/wordpress/wp-contentから変更する。
define('WPLANG', 'ja'); # 日本語表示にする
define('FS_METHOD', 'direct');  # プラグインやテーマを更新するための設定
```
apache2を再起動する。
```
$ sudo service apache2 reload
```
