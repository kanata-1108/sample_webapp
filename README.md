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
```