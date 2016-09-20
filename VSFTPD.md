# Vsftpd

## Vsfrpdのインストールと設定

### インストール
```
$ sudo apt install vsftpd
```

### 設定

```
$ sudo vim /etc/vsftpd.conf

write_enable=YES  # コメントをはずす

ascii_upload_enable=YES # コメントをはずす
ascii_download_enable=YES # コメントをはずす

chroot_local_user=YES # コメントをはずす
chroot_list_enable=YES # コメントをはずす

chroot_list_file=/etc/vsftpd.chroot_list # コメントをはずす

ls_recurse_enable=YES # コメントをはずす

utf8_filesystem=YES
local_root

```
ユーザのリスト
```
$ sudo vim /etc/vsftpd.chroot_list

hiroto
```
vsftpdの再起動
```
$ sudo service vsftpd restart
```
