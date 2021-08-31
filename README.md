Docker for Mac で Ruby on Rails アプリの開発環境

## Railsアプリ用のディレクトリを作成
workディレクトリ(なんでも構いません)を作成して
```
mkdir work
```
```
cd work
```
githubからクローンしてくる
```
git clone https://github.com/nuovotaka/docker-rails_v6-nginx.git
```
```
cd docker-rails_v6-nginx
```

## コンテナにRailsアプリを作成

「docker-compose run」コマンドで「app」コンテナに「rails new」を行う。
```
docker-compose run --rm app rails new . --force --database=mysql --skip-bundle
```
実行後、ディレクトリの中にRailsのファイル群が作成されている

## Gemfileの変更

バージョン情報を削除する
```
gem 'mysql2'
```

Gemfileの変更を適用するためには以下のコマンドを実行
```
docker-compose build
```

## puma.rbをconfig/以下へ設置
```
cp docker/rails/puma.rb config/
```

## config/database.ymlの設定

config/database.yml内の記述を下記のように変更する
```
default: &default
  adapter: mysql2
  encoding: utf8mb4
  pool: <%= ENV.fetch("RAILS_MAX_THREADS")  { 5 } %>
  username: root
  password: <%= ENV['MYSQL_ROOT_PASSWORD'] %>
  host: db

```

## コンテナの実行＆ブラウザの確認

最初はWebpackのインストールとデータベースを作成する（次回からは必要ない）
```
docker-compose run --rm app bundle exec rails webpacker:install
```
```
docker-compose run --rm app rake db:create
```

### コンテナの実行

```
docker-compose up
```

### コンテナの確認

```
docker-compose ps
```

localhost でRailsのウェルカム画面が表示されるのがわかると思う。

## Rails アプリを作成する方法

例　メモアプリの作成
別タブで以下のコマンドを実行する
```
docker-compose run app rails g scaffold Memo title:string body:text
```
```
docker-compose run app rake db:migrate
```

基本的にRailsコマンドの最初に「docker-compose run app」をつけてやればコンテナ上で実行され、Mac側にも反映されます。

### アプリの確認

localhost/memos でアクセスするとアプリが作成されているのがわかる。

## 注意点

Gemfileを変更したら、buildでイメージ作成してから、コンテナ実行すること
```
$ docker-compose build
$ docker-compose up
```

## コンテナの停止と削除
DashboardのContainers/Appsで停止と削除を行う

## コンテナのイメージの削除
DashboardのImagesでイメージの削除を行う

## mysqlへの接続
[TablePlus](https://tableplus.com/)を使ってデータベースを確認(Mac)
