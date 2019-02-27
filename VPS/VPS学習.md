# VPS
- 仮想専用サーバー

## 初期設定
- 接続

```
local
$ ssh root@(IPAddress)
```
ssh通信（セキュアシェル）で接続

- 一般ユーザー追加
```
vps操作
ユーザー追加
$ adduser 名前
ユーザーにパスワード設定
$ passwd 名前
wheelグループ(sudoできる)に追加
$ gpasswd -a 名前 wheel
確認
$ su 名前
```
この時点で新規追加したユーザーでSSHでログインできる

- 共通暗号鍵
配置場所とパーミッション
```
vps
$ mkdir .ssh
$ chmod 700 .ssh
```
認証設定変更
```
vps
$ sudo vim /etc/ssh/sshd_config
中身
#公開鍵認証を許可
PubkeyAuthentication yes

#clientの公開鍵の場所と名前を指定
AuthorizedKeysFile      .ssh/authorized_keys

#rootログインを無効化
PermitRootLogin no

#パスワードなしでのログイン無効化
PermitEmptyPasswords no
反映
$ sudo systemctl restart sshd.service
```
鍵生成
```

$ cd .ssh
キー生成　タイプ rsa 長さ 2048
$ ssh-keygen -t rsa -b -2048 
pubキーアップロード
$ scp ◯◯.pub (UserName)@(IPAddress):~/.ssh/authorized_keys
```
config作成
```
$ vim .ssh/config
中身
Host alias
     HostName (IPAddress)
     Port 22
     User (UserName)
     IdentityFile (PathToSecretKey)
```
これで`$ssh alias`で接続できるようになった

-セキュリティ強化

```

$ sudo vim /etc/ssh/sshd_config
#ポート変更
Port 10022
#パスワードでのログイン無効化
PasswordAuthentication no
firewallの変更
$ sudo vim /etc/firewalld/zone/public.xml

<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="dhcpv6-client"/>
  <service name="ssh"/>
  <service name="mysql"/>
  <port protocol="tcp" port="1022"/> # ココを追加
</zone>
両方再起動
$ sudo systemctl restart sshd.service
$ sudo systemctl restart firewalld.service
※.ssh/configの中身か着替えるの忘れないこと
```

### 参考
https://qiita.com/EisKern/items/3a12bd61ba572e8430d9#firewalld%E3%81%A7%E8%A8%AD%E5%AE%9A

https://qiita.com/kenokada/items/105007df710adec237db#ssh%E3%81%AE%E3%83%9D%E3%83%BC%E3%83%88%E7%95%AA%E5%8F%B7%E3%82%92%E5%A4%89%E6%9B%B4

# Apache PHP MySQL(MariaDB)
centos7ならこれでいける
https://support.conoha.jp/vps/school/hellovps02/?btn_id=school-hellovps03-sidebar_school-hellovps02#sec06

# yum

```
yumのサブコマンド
コマンド	意味
install	パッケージのインストール
update	パッケージのアップデート
check-update	アップデートのチェック
remove	パッケージの削除
list	利用可能なパッケージのリスト
info	パッケージの情報を表示する
groupinstall	パッケージグループのインストール
groupupdate	パッケージグループのアップデート
grouplist	パッケージグループのリスト表示
groupinfo	パッケージグループの情報を表示
search	指定した文字列を含むパッケージを検索する
localinstall	ローカルのrpmファイルでインストールする
localupdate	ローカルのrpmファイルでアップデートする
```
yumのオプション(https://uxmilk.jp/9899)
yum -y は全部yes

### ドキュメントルート変更
初期は
`/var/www/htm`
管理しているのは
`/etc/httpd/conf/httpd.conf`
の
`DocumentRoot "/var/www/html"`
を変更する
先に変更するディレクトリが存在しないとエラーになる
```
Job for httpd.service failed because the control process exited with error code. See "systemctl status httpd.service" and "journalctl -xe" for details.
```
とかいうのがでてたら
```
sudo service httpd configtest
```
でエラーが出てないか確認できるのでそこを治す。
