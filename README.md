## Use Webapp

git clone後に以下のコマンドを実行してPHPとMariaDB、phpmyadominコンテナのイメージをビルドする
```
docker-compose up -d --build
```
git cloneだけだと必要なファイルが存在しないためビルド後に以下の手順で作成する
```
docker container exec -it laravel_container bash
composer install
```

srcディレクトリ下に環境変数を格納する.envがないため、.env.sampleの内容をコピーしてDB関連の環境変数を以下のように書き換え

```
cp .env.example .env
```
```txt:.env

DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=test_db
DB_USERNAME=test
DB_PASSWORD=testpass
```

[http://localhost:80](http://localhost:80)にアクセスしてlalavelのWelcomeページが表示されれば成功

今の状態だと、`docker-compose.yml`のdbコンテナに記述されたtest_dbが作成されていないことに加え、ユーザであるtestにアクセス権が付与されていない。
以下のコマンドを実行して順に処理していく。

mariadbコンテナに入り、rootでmariadbにアクセス。パスワードは`docker-compose.yml`に記述してある`rootpass`
```
docker container exec -it mariadb_container bash
mariadb -u root -p
```

`test_db`データベースを作成し、作成されたことを確認。
```
CREATE DATABASE test_db;
show databases;
```

`docker-compose.yml`に記述してあるユーザ`test`にアクセス権を付与し、有効化する。
```
GRANT ALL PRIVILEGES ON * . * TO 'test'@'%';
FLUSH PRIVILEGES;
```

コンテナから抜けて`docker-compose up -d --build`で再構築する。

laravelコンテナに入って`test_db`をlaravelの機能であるマイグレーションで管理できるようにする。
```
docker container exec -it laravel_container bash
php artisan migrate
```