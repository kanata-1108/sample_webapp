## Use Webapp

git clone後に以下のコマンドを実行してPHPとMariaDB、phpmyadominコンテナのイメージをビルドする
```
docker-compose up -d --build
```
git cloneだけだと必要なファイルが存在しないためビルド後に以下の手順で作成する
```
docker container exec -it php_container bash
conposer install
conposer update
exit
```
[http://localhost:80](http://localhost:80)にアクセスしてlalavelのWelcomeページが表示されれば成功

srcディレクトリ下に環境変数を格納する.envがないため作成。作成後コピペ
<details>

<summary>./src/.env</summary>

```
APP_NAME=Laravel
APP_ENV=local
APP_KEY=base64:fOI0U6dDAKbcKgY3HWmAPW50wiG8rG4RIZbIc9Ygc54=
APP_DEBUG=true
APP_URL=http://localhost

LOG_CHANNEL=stack
LOG_DEPRECATIONS_CHANNEL=null
LOG_LEVEL=debug

DB_CONNECTION=mysql
DB_HOST=db
DB_PORT=3306
DB_DATABASE=test_db
DB_USERNAME=test
DB_PASSWORD=testpass

BROADCAST_DRIVER=log
CACHE_DRIVER=file
FILESYSTEM_DISK=local
QUEUE_CONNECTION=sync
SESSION_DRIVER=file
SESSION_LIFETIME=120

MEMCACHED_HOST=127.0.0.1

REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379

MAIL_MAILER=smtp
MAIL_HOST=mailpit
MAIL_PORT=1025
MAIL_USERNAME=null
MAIL_PASSWORD=null
MAIL_ENCRYPTION=null
MAIL_FROM_ADDRESS="hello@example.com"
MAIL_FROM_NAME="${APP_NAME}"

AWS_ACCESS_KEY_ID=
AWS_SECRET_ACCESS_KEY=
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=
AWS_USE_PATH_STYLE_ENDPOINT=false

PUSHER_APP_ID=
PUSHER_APP_KEY=
PUSHER_APP_SECRET=
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https
PUSHER_APP_CLUSTER=mt1

VITE_PUSHER_APP_KEY="${PUSHER_APP_KEY}"
VITE_PUSHER_HOST="${PUSHER_HOST}"
VITE_PUSHER_PORT="${PUSHER_PORT}"
VITE_PUSHER_SCHEME="${PUSHER_SCHEME}"
VITE_PUSHER_APP_CLUSTER="${PUSHER_APP_CLUSTER}"
```

</details>

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