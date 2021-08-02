# 08_docker-compose

## Docker-composeとは
- コンテナをまとめて起動する→web、DB、キャッシュなどの開発環境やテスト環境を早期に立ち上げれたりする。

## Docker-compose実行の手順
1. Dockerfileを用意するか、使用するイメージをDockerHubなどに用意する
2. docker-compose.ymlを定義する
3. docker-compose upを実行する（※-d：バックグラウンド実行のためにデタッチドモードで起動したりする）

## Docker-compose.ymlの定義
version:'3'　　←Docker-composeファイル形式のバージョン<br>
services:<br>
    web:　←サービス名（１つのサービスで１つのコンテナが起動する）<br>
        build:.　　←buildコンテキスト(docker-compose.ymlと同じディレクトリ)<br>
        ports:<br>
        - "5000:5000"<br>
        volumes:<br>
        - .:code<br>
        - logvolume01:var/log<br>
        link:<br>
        - redis<br>
    redis:　←サービス名<br>
        image:redis<br>
volumes:<br>
    logvolume01:{}<br>

## docker-composeの各種コマンド
- `docker-compose up -d`：デタッチドモードで起動
- `docker-compose down -v`：コンテナを削除（-v:で関連するボリュームも削除）
- `docker-compose ps`：コンテナ状態を確認
- `docker-compose run` [サービス名] [コマンド]:特定のサービスでコマンドを実行
- `docker-compose stop`:コンテナを一時停止
- `docker-compose start`:コンテナを再開