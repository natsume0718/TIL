# laravel

## composer install & laravel install

```
php -v
php -m | grep -e openssl -e PDO -e mbstring -e tokenizer -e ^xml$ -e ctype -e json -e bcmath
yum -y install composer
(失敗したら)
# tmpディレクトリへ移動
cd /tmp
# composerのインストーラーをダウンロードする
curl -sS https://getcomposer.org/installer | php
(curlはHTTPアクセスをしてコンテンツを取得できるコマンド)
(-sSはダウンロード進捗とエラー表示)
# composerを移動させる
mv /tmp/composer.phar /usr/local/bin/composer
# composerがインストールされているか確認
composer -v
# ディレクトリ移動
cd /root/.config/composer
# Composerを使用し、Laravelインストーラをダウンロード
composer global require "laravel/installer"
# パスを通す
export PATH="~/.config/composer/vendor/bin:$PATH"
```
pathについては(https://qiita.com/Naggi-Goishi/items/2c49ea50602ea80bf015)
