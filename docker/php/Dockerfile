FROM php:8.0-apache

# 各種設定ファイルをコンテナに反映
ADD php.ini /usr/local/etc/php/
ADD 000-default.conf /etc/apache2/sites-enabled/

# 各種ミドルウェアのインストール
RUN apt-get update \
&& apt-get install -y \
git \
unzip \
vim \
# phpのzip拡張に必要なライブラリ
zlib1g-dev \
# zipファイルの読み書きができるライブラリ
libzip-dev \
# PNG画像を読み込み・書き込み・編集するためのライブラリ
libpng-dev \
# SQL操作に関するライブラリとzipライブラリ
&& docker-php-ext-install pdo_mysql mysqli zip

# Composerのインストール
RUN cd /usr/bin && curl -s http://getcomposer.org/installer | php && ln -s /usr/bin/composer.phar /usr/bin/composer

# Composerイメージを実行形式にしてコピー
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# a2emod rewrite をして apache に rewrite モジュールを追加
RUN /bin/sh -c a2enmod rewrite

WORKDIR /var/www/html/myapp/
# RUN composer create-project "laravel/laravel" ./
# RUN chown www-data storage/ -R
