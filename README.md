Laravelプロジェクトの開発をしている時にユーザーの位置情報を管理したくなりました。
LaradockではPostgreSQLと地理空間情報を扱えるPostgisを公式にサポートしてあるようなのでこれを使いたいと思います。

流れとしては以下の感じです。
1. Laradockプロジェクトをインストール(githubからダウンロード)
2. Laradockの`.env`を編集
3. `docker-compose.yml`の編集
4. dockerを立ち上げ、ゲストにログイン
5. composerでlaravelのインストール
6. laravelの`.env`ファイルを編集


## 1. Laradockのプロジェクトをインストール

まずはGithubからLaradockのプロジェクトをインストールします。
`git init`してローカルリポジトリを作ってからgithubからLaradockのプロジェクトをクローンします。
そしてLaravelのソースを管理するディレクトリ`src`を作成します。

```bash
git init
git submodule add https://github.com/Laradock/laradock.git
mkdir src
```

そしたら、サンプルの`.env`を複製して編集します。

```bash
cd laradock
cp env-example .env
vim .env
```

## 2. Laradockの`.env`を編集

以下を変更する

```bash
ホスト上のソースのパスを選択
# APP_CODE_PATH_HOST
APP_CODE_PATH_HOST=../src

dockerマシン上のストレージのパスを選択
# DATA_PATH_HOST=~/.laradock/data
DATA_PATH_HOST=../.laradock/data

# MYSQL入らないので消す
# PHP_FPM_INSTALL_MYSQLI=true		
PHP_FPM_INSTALL_MYSQLI=false


# PHP_FPM_INSTALL_PGSQL=false		
PHP_FPM_INSTALL_PGSQL=true

# PHP_FPM_INSTALL_PG_CLIENT=false		
PHP_FPM_INSTALL_PG_CLIENT=true

# PHP_FPM_INSTALL_POSTGIS=false		
PHP_FPM_INSTALL_POSTGIS=true

# PHP_WORKER_INSTALL_PGSQL=false
PHP_WORKER_INSTALL_PGSQL=true
```


## 3. `docker-compose.yml`の編集

`docker-compose.yml`では記述されている `depends_on`ディレクティブの指定により、依存関係にあるサービスを立ち上げます。
今回はPostgreSQLのクライアント`pgAdmn`を使うのですが、`depends_on`が`postgres`となっているので`postgres-postgis`に修正する必要があります。  
したがってlaradockディレクトリの`docker-compose.yml`を編集します。

```
# 694行目あたりのpgAdminの依存関係を変更
### pgAdmin ##############################################
    pgadmin:
      image: dpage/pgadmin4:latest
      environment:
        - "PGADMIN_DEFAULT_EMAIL=${PGADMIN_DEFAULT_EMAIL}"
        - "PGADMIN_DEFAULT_PASSWORD=${PGADMIN_DEFAULT_PASSWORD}"
      ports:
        - "${PGADMIN_PORT}:80"
      volumes:
        - ${DATA_PATH_HOST}/pgadmin:/var/lib/pgadmin
      depends_on:
      #  - postgres を以下のように変更
         - postgres-postgis
      networks:
        - frontend
        - backend
```


## 4. dockerを立ち上げ、ゲストにログイン

立ち上げる時は以下のコマンドで実行する

```
docker-compose up -d nginx postgres-postgis pgadmin
```
これでエラーが出ずに立ち上げに成功すると思います。
そしたらゲストにログインします。

```
docker-compose exec workspace bash
```


## 5.composerでlaravelのインストール

ゲスト側のターミナルでcomposerを使ってLaravelをインストールします。
ついでにcomposerの高速化もしておきます。


```bash
# omposer の高速化
# ミラーサーバをpackagist.jpに変更
composer config -g repos.packagist composer https://packagist.jp

# 高速化プラグイン
composer global require hirak/prestissimo

composer create-project --prefer-dist laravel/laravel ./
# 5.5.*系(LTS)のバージョンにしたい際はこちら
composer create-project --prefer-dist laravel/laravel ./ 5.5.*

# インストールしたら
exit
```

## 6. laravelの`.env`ファイルを編集

laravel自体の`.env`ファイルを編集する
一旦dockerをストップする

```
docker-compose stop
```

以下のように変更すればいいと思う

```
DB_CONNECTION=pgsql
DB_HOST=postgres-postgis
DB_PORT=5432
DB_DATABASE=default
DB_USERNAME=default
DB_PASSWORD=secret
```

これで再び立ち上げてゲストにログインすれば`postgreSQL`と`postgis`が使えるようになっているはず。

```
# dockerの立ち上げ
docker-compose up -d nginx pgadmin postgres-postgis

# ゲストにログイン
docker-compose exec workspace bash

# マイグレーションの実行
root@58ee3cacece9:/var/www# php artisan migrate
Migration table created successfully.
Migrating: 2014_10_12_000000_create_users_table
Migrated:  2014_10_12_000000_create_users_table (0.04 seconds)
Migrating: 2014_10_12_100000_create_password_resets_table
Migrated:  2014_10_12_100000_create_password_resets_table (0.03 seconds)

# Auth機能の実装
root@58ee3cacece9:/var/www# php artisan make:auth
Authentication scaffolding generated successfully.
```

これでおっけい

最後に`postgis`がインストールされているか確認する

```bash

psql -h postgres-postgis -U default
Password for user default: secret # パスワードを入力
# psql (10.10 (Ubuntu 10.10-1.pgdg16.04+1), server 11.2 (Debian 11.2-1.pgdg90+1))
# WARNING: psql major version 10, server major version 11.
#          Some psql features might not work.
# Type "help" for help.

# \dxで拡張モジュールを確認できる
default=# \dx
                                            List of installed extensions
          Name          | Version |   Schema   |                             Description                             
------------------------+---------+------------+---------------------------------------------------------------------
 fuzzystrmatch          | 1.1     | public     | determine similarities and distance between strings
 plpgsql                | 1.0     | pg_catalog | PL/pgSQL procedural language
 postgis                | 2.5.2   | public     | PostGIS geometry, geography, and raster spatial types and functions
 postgis_tiger_geocoder | 2.5.2   | tiger      | PostGIS tiger geocoder and reverse geocoder
 postgis_topology       | 2.5.2   | topology   | PostGIS topology spatial types and functions
(5 rows)
```

こんな感じ。
                                            <p style="text-align:center;">***List of installed extensions***</p>

|          Name          | Version |   Schema   |                             Description                             |
|------------------------|---------|------------|--------------------------------------------------------------------|
| fuzzystrmatch          | 1.1     | public     | determine similarities and distance between strings|
| plpgsql                | 1.0     | pg_catalog | PL/pgSQL procedural language|
| postgis                | 2.5.2   | public     | PostGIS geometry, geography, and raster spatial types and functions|
| postgis_tiger_geocoder | 2.5.2   | tiger      | PostGIS tiger geocoder and reverse geocoder|
| postgis_topology       | 2.5.2   | topology   | PostGIS topology spatial types and functions|
(5 rows)

## 終わり

LaradockでPostGISを使いたかったのですが、Laradockで「サポートしているよ！」って書いてあるだけで、ドキュメントを探してもやり方が見付からずかなり苦戦しました。
色々探しても「`docker-compse up postgres-postgis`にすれば使えるよ。」といった情報くらいで、結局うまくいかなかったのでこの記事を作成しました。

そもそもPostGISってあまり使われないのでしょうか？🤔
それとも通常はエラーが生じるようなものでもないのでしょうか？🤔
わたし、気になります

## おまけ

### LaravelでPHPのバージョンを変更する時のやり方

PostGISを導入するついでにPHPのバージョンも変更したのでメモ

PHPのバージョンの指定は、`laradock/.env`で行う
今回はPHP7.2から7.3に変更した

```bash
### PHP Version ###########################################

# Select a PHP version of the Workspace and PHP-FPM containers (Does not apply to HHVM). Accepted values: 7.3 - 7.2 - 7.1 - 7.0 - 5.6
# PHP_VERSION=7.2
PHP_VERSION=7.3
```
編集したら、php-fpmをbuildする

```bash
docker-compose build php-fpm
```
これで再び通常どうり起動できるはず。

```
docker-compose up -d nginx pgadmin postgres-postgis
```
