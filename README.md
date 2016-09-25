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
.htaccess Overridesを有効にする。
```
$ sudo vim /etc/apache2/apache2.conf
<Directory /var/www/html/>
  AllowOverride All
</Directory>
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
wordpress.orgの最新版をインストールする場合
### ダウンロード・解凍
```
$ cd /tmp
$ curl -O https://wordpress.org/latest.tar.gz
or
$ wget http://wordpress.org/latest.tar.gz
$ tar xzvf latest.tar.gz
$ touch /tmp/wordpress/.htaccess
$ chmod 660 /tmp/wordpress/.htaccess
$ cp /tmp/wordpress/wp-config-sample.php /tmp/wordpress/wp-config.ph
$ mkdir /tmp/wordpress/wp-content/upgrade
$ sudo cp -a /tmp/wordpress/. /var/www/html/blog
$ sudo chown -R hiroto:www-data /var/www/html/blog
wordpressをアップグレードするときは一時的に
$ sudo chown -R www-data /var/www/html/blog
wordpressのアップグレードが終わったら
$ sudo chown -R hiroto /var/www/html/blog
$ sudo find /var/www/html/blog -type d -exec chmod g+s {} \;
$ sudo chmod g+w /var/www/html/blog/wp-content
$ sudo chmod -R g+w /var/www/html/blog/wp-content/themes
$ sudo chmod -R g+w /var/www/html/blog/wp-content/plugins
$ curl -s https://api.wordpress.org/secret-key/1.1/salt/

```

### データベースとユーザの作成
MySQLクライアントを利用します。


databasename: wordpress,wordpressusername: wordpress, hostname: localhost, password: xxxxxxx
```
$ mysql -u root -p
mysql> CREATE DATABASE databasename;
or
mysql> CREATE DATABASE databasename DEFAULT CHARACTER SET urf8 COLLATE utf8_unicode_ci;

mysql> GRANT ALL PRIVILEGES on databasename.* TO "wordpressusername"@"hostname"
    -> IDENTIFIED BY "password";
or 
mysql> CRANT ALL ON wordpress.* TO 'wordpressusername'@'localhost' IDENTIFIED BY 'password';

mysql> FLUSH PRIVILEGES;
mysql> EXIT
BYE
$
```

### wp-config.phpの設定
データベース情報と秘密鍵の値を記入。

```
$ cp wp-config-sample.php wp-config.php
```
wp-config.phpの編集
```
define('DB_NAME', 'wordpress');
define('DB_USER', 'wordpress');
define('DB_PASSWORD', 'xxxxxxx');
define('DB_HOST', 'localhost');
define('DB_CHARSET', '');
define('DB_COLLATE', '');

define('FS_METHOD', 'direct');
define('WPLANG', 'ja');
```

### ファイルのアップロード
```
$ sudo cp -fr wordpress/* /var/www/html/blog
```

### インストールスクリプトの実行
ブラウザでwp-admin/install.phpへアクセスし、インストールスクリプトを実行。
ルートディレクトリにWordPressファイルを設置した場合
```
http://example.com/wp-admin/install.php
```
blogというサブディレクトリにWordPressファイルを設置した場合
```
http://example.com/blog/wp-admin/install.php
```
サイト名、パスワード、メールアドレスを入力する。

## Ubuntuのリポジトリを利用する場合
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
