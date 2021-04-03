# README

This README would normally document whatever steps are necessary to get the
application up and running.

Things you may want to cover:

* Ruby version:6.0

* System dependencies

* Configuration

* Database creation

* Database initialization

* How to run the test suite

* Services (job queues, cache servers, search engines, etc.)

* Deployment instructions

* ...


MacでDockerを使用して、Rails環境を構築

## 構築手順
### 1. プロジェクトディレクトリの作成・移動
```
$ mkdir project-name
$ cd project-name
```
*project-nameは任意

### 2. 環境に合わせてポートを修正
docker-compose.ymlで下記の箇所を環境に合わせて記述します。
ex)
8行目 Webアクセス用ポート  
80:80  
27行目 DBアクセス用ポート  
3306:3306

### 3. Dockerfileの作成・編集
```
$ vi Dockerfile
```


### 4. Gemfileの作成・編集
```
$ vi Gemfile
```
*インストールするRailsのバージョンを指定します。
ここではRails 6に対応するようにしています。



### 5. Gemfile.lockの作成（中身はそのまま）
```
$ vi  Gemfile.lock
```

### 6. entrypoint.shの作成
```
$ vi  entrypoint.sh
```
*set -e ：　「エラーが発生するとスクリプトを終了する」の意
rm ...　：　「railsのpidが存在している場合に削除する」処理
exec "$@"　：　DockerfileのCMDで渡されたコマンド（→Railsのサーバー起動）を実行

### 7. docker-compose.ymlの作成
```
$ vi  docker-compose.yml
```
*[version] docker-composeのバージョンです。2019年10月現在、最新は'3'


~プロジェクトのビルド
### 8. Docker環境を構築する
```
[postgresql]
$ docker-compose run web rails new . --force --no-deps --database=postgresql --skip-bundle
[MYSQL]
$ docker-compose run web rails new . --force --database=mysql --skip-bundle
```
＊--force　：　既存のGemfileを上書きするためのオプション
--no-deps　：　リンクしたサービスを起動しない
--database=postgresql or mysql　：　DBを指定
--skip-bundle　：　bundleをskip（最後にbundleします）

### 9. /config/database.ymlの修正 
・ユーザー情報やパスワード、サービス名を修正


### 10. 一つ上の階層に戻り、Dockerコンテナをビルド
```
$ docker-compose build　--no-cache
```

### 11. コンテナの起動
```
$ docker-compose up
```

### 12. DB作成
/src/project-name/以下にlaravelが構築されますので、階層を1つ上へ移動します。
```
docker-compose run web rails db:create
```

### Railsの動作確認
ブラウザで http://localhost:3000(ymlファイルのwebサービスのポート) へアクセスしRailsの初期ページが表示されれば、ひとまず環境構築は完了。  

### データベースに直接接続する
Mysql workbenchなどでdockerで起動したmysqlへ接続する場合は以下の設定で接続できます。  
ホスト　0.0.0.0  
ポート　3306  
ユーザー名　root  
パスワード　root
