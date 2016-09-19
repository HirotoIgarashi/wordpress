# wordpress
Ubuntuにwordpressをインストールする。

## 前提
LAMP(Linux+Apache+MySQL+PHP)の環境
taskselのインストール
```
$ sudo apt install tasksel
```
taskselの起動とLAMP serverのインストール
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
インストールされたWordPressのプログラムのシンボリックリンクをapache2のルートディレクトリの下に作成する。これによりhttp://localhost/blogでアクセスできるようになる。
```
$ sudo ln -s /usr/share/wordpress /var/www/html/blog
```
/usr/share/doc/wordpress/README.Debianの内容に従いMySQLの設定をする。下の例だとMySQLのユーザ名とテーブル名をwordpressとしている。またhttp://localhostでアクセスできるように設定する。/etc/wordpress/config-localhost.phpが作成される。
```
$ cd /usr/share/doc/wordpress/examples
$ sudo gzip -d /usr/share/doc/wordpress/examples/setup-mysql.gz
$ sudo bash /usr/share/doc/wordpress/examples/setup-mysql -n wordpress localhost
```
## WordPressの設定
/etc/wordpress/config-localhost.phpを編集する。日本語表示にしたいので以下の行を追加する。
```
define('WPLANG', 'ja'); # 言語を日本語に
```
apache2を再起動する。
```
$ sudo service apache2 reload
```
