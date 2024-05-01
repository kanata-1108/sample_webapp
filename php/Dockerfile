FROM php:8.0-apache

# PHPとApacheの設定ファイルをコンテナに反映
ADD php.ini /usr/local/etc/php/
ADD ./config/000-default.conf /etc/apache2/sites-enabled/

# 各種ミドルウェアのインストール
RUN apt-get update \
&& apt-get install -y git unzip vim \
# phpのzip拡張に必要なライブラリ
zlib1g-dev \
# zipファイルの読み書きができるライブラリ
libzip-dev \
# PNG画像を読み込み・書き込み・編集するためのライブラリ
libpng-dev \
# SQL操作に関するライブラリとzipライブラリ
&& docker-php-ext-install pdo_mysql zip

# # Composerのインストール
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer
ENV COMPOSER_ALLOW_SUPERUSER: 1

# a2emod rewrite をして apache に rewrite モジュールを追加
RUN /bin/sh -c a2enmod rewrite

WORKDIR /var/www/html