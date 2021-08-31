# Herokuにデプロイ
## ref) https://qiita.com/sasasoni/items/d93224e9b62b524b1dc6
<p style='text-align: right;'> &copy; 20210831 by Takanori Shima </p>

```
* 以下、Cloud9上でターミナルを起動してbbsフォルダ内でコマンドを打つ *
```

## 1. コマンドラインツールのインストール
### ref) https://qiita.com/sasasoni/items/d93224e9b62b524b1dc6

```
wget https://cli-assets.heroku.com/heroku-linux-x64.tar.gz -O heroku.tar.gz
sudo mkdir -p /usr/local/lib/heroku
sudo tar --strip-components 1 -zxvf heroku.tar.gz -C /usr/local/lib/heroku
sudo ln -s /usr/local/lib/heroku/bin/heroku /usr/local/bin/heroku
```

## 2. Heroku にログイン
```
heroku login -i
```

## 3. Herokuアプリ作成
```
heroku create quark2galaxy-laravel-bbs
```

## 4. Herokuアプリ一覧取得
```
heroku apps
```
>=== quark2galaxy@gmail.com Apps<br>
>quark2galaxy-laravel-bbs

## 5. リモートリポジトリ heroku の確認
```
git remote -v
```
> heroku  https://git.heroku.com/quark2galaxy-laravel-bbs.git (fetch)<br>
> heroku  https://git.heroku.com/quark2galaxy-laravel-bbs.git (push)<br>
> origin  https://github.com/TakanoriShima/bbs_laravel_5.8_new.git (fetch)<br>
> origin  https://github.com/TakanoriShima/bbs_laravel_5.8_new.git (push)

## 6. Heroku 用設定ファイルを新規作成
```
echo "web: vendor/bin/heroku-php-apache2 public/" > Procfile
```

## 7. Heroku アプリの環境変数の設定
```
heroku config:set APP_KEY=$(php artisan --no-ansi key:generate --show)
```
>Setting APP_KEY and restarting ⬢ quark2galaxy-laravel-bbs... done, v3<br>
>APP_KEY: base64:Nl9uPLdkE20+6OLEYT80mEvLlHehUlkqMaM3lHhmPkA=

## 8. 環境変数の確認方法
```
heroku config
```
>=== quark2galaxy-laravel-bbs Config Vars<br>
>APP_KEY: base64:Nl9uPLdkE20+6OLEYT80mEvLlHehUlkqMaM3lHhmPkA=

## 9. Heroku 上でPostgreSQLデータベースを作成
```
heroku addons:create heroku-postgresql:hobby-dev
```
>Creating heroku-postgresql:hobby-dev on ⬢ quark2galaxy-laravel-bbs... free
>Database has been created and is available
> ! This database is empty. If upgrading, you can transfer<br>
> ! data from another database with pg:copy<br>
>Created postgresql-perpendicular-61502 as DATABASE_URL<br>
>Use heroku addons:docs heroku-postgresql to view documentation<br>

## 10. 再度、環境変数の確認方法
```
heroku config
```
>=== quark2galaxy-laravel-bbs Config Vars<br>
>APP_KEY:      base64:Nl9uPLdkE20+6OLEYT80mEvLlHehUlkqMaM3lHhmPkA=<br>
>DATABASE_URL: postgres://ygtjmxlenghsoc:32d35519690b4a91f35fffda64c8b3748bdda8dd8153fe04da013b5483ad1356@ec2-52-72-125-94.compute-1.amazonaws.com:5432/dctg5apf3kva5c

## 11. データベース用環境設定
```
一般書式)
DATABASE_URL: postgres://ユーザ名:パスワード@ホスト名:5432/データベース名
今の設定)
DATABASE_URL: postgres://ygtjmxlenghsoc:32d35519690b4a91f35fffda64c8b3748bdda8dd8153fe04da013b5483ad1356@ec2-52-72-125-94.compute-1.amazonaws.com:5432/dctg5apf3kva5c

見比べて以下の対応が付く
ユーザ名: ygtjmxlenghsoc
パスワード: 32d35519690b4a91f35fffda64c8b3748bdda8dd8153fe04da013b5483ad1356
ホスト名: ec2-52-72-125-94.compute-1.amazonaws.com
データベース名: dctg5apf3kva5c

そして
一般書式)
heroku config:set DB_CONNECTION=pgsql DB_USERNAME=ユーザ名 DB_PASSWORD=パスワード DB_HOST=ホスト名 DB_DATABASE=データベース名

に当てはめて以下の作成し、実行する

heroku config:set DB_CONNECTION=pgsql DB_USERNAME=ygtjmxlenghsoc DB_PASSWORD=32d35519690b4a91f35fffda64c8b3748bdda8dd8153fe04da013b5483ad1356 DB_HOST=ec2-52-72-125-94.compute-1.amazonaws.com DB_DATABASE=dctg5apf3kva5c

```
> Setting DB_CONNECTION, DB_USERNAME, DB_PASSWORD, DB_HOST, DB_DATABASE and restarting ⬢ quark2galaxy-laravel-bbs... done, v6<br>
> DB_CONNECTION: pgsql<br>
> DB_DATABASE:   dctg5apf3kva5c<br>
> DB_HOST:       ec2-52-72-125-94.compute-1.amazonaws.com<br>
> DB_PASSWORD:   32d35519690b4a91f35fffda64c8b3748bdda8dd8153fe04da013b5483ad1356<br>
> DB_USERNAME:   ygtjmxlenghsoc

## 12. Git
```
git add .
git commit -m 'Heroku用にProcfileを追加'
git push origin main
git push heroku main
```

## 13. Heroku上でマイグレーション実施
```
heroku run php artisan migrate
```
> Do you really wish to run this command? (yes/no) [no]:<br>
> > yes<br>
><br>
>Migration table created successfully.<br>
>Migrating: 2014_10_12_000000_create_users_table<br>
>Migrated:  2014_10_12_000000_create_users_table (0.03 seconds)<br>
>Migrating: 2014_10_12_100000_create_password_resets_table<br>
>Migrated:  2014_10_12_100000_create_password_resets_table (0.02 seconds)<br>
>Migrating: 2021_08_30_152643_create_profiles_table<br>
>Migrated:  2021_08_30_152643_create_profiles_table (0.06 seconds)<br>
>Migrating: 2021_08_30_162219_create_posts_table<br>
>Migrated:  2021_08_30_162219_create_posts_table (0.02 seconds)<br>
>Migrating: 2021_08_30_172053_create_comments_table<br>
>Migrated:  2021_08_30_172053_create_comments_table (0.03 seconds)<br>
>Migrating: 2021_08_30_185847_create_favorites_table<br>
>Migrated:  2021_08_30_185847_create_favorites_table (0.06 seconds)<br>
>Migrating: 2021_08_31_110656_create_user_follow_table<br>
>Migrated:  2021_08_31_110656_create_user_follow_table (0.03 seconds)<br>

## 14. Cloud9にPostgreSQLをインストール
```
sudo yum install postgresql95-devel postgresql95-server postgresql95-contrib
psql --version
```
> psql (PostgreSQL) 9.5.24

## 15. Heroku上の PostgreSQLにログインしてテーブルができていることを確認する
### ref) https://qiita.com/aosho235/items/c657e2fcd15fa0647471
```
heroku pg:psql

--> Connecting to postgresql-perpendicular-61502
psql (9.5.24, server 13.4 (Ubuntu 13.4-1.pgdg20.04+1))
WARNING: psql major version 9.5, server major version 13.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

quark2galaxy-laravel-bbs::DATABASE=> \c dctg5apf3kva5c
psql (9.5.24, server 13.4 (Ubuntu 13.4-1.pgdg20.04+1))
WARNING: psql major version 9.5, server major version 13.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
You are now connected to database "dctg5apf3kva5c" as user "ygtjmxlenghsoc".
quark2galaxy-laravel-bbs::DATABASE=> \dt
                 List of relations
 Schema |      Name       | Type  |     Owner      
--------+-----------------+-------+----------------
 public | comments        | table | ygtjmxlenghsoc
 public | favorites       | table | ygtjmxlenghsoc
 public | migrations      | table | ygtjmxlenghsoc
 public | password_resets | table | ygtjmxlenghsoc
 public | posts           | table | ygtjmxlenghsoc
 public | profiles        | table | ygtjmxlenghsoc
 public | user_follow     | table | ygtjmxlenghsoc
 public | users           | table | ygtjmxlenghsoc

quark2galaxy-laravel-bbs::DATABASE=> \q
 
```

## 16. S3 設定
```
heroku config:set AWS_ACCESS_KEY_ID="AKIASNU7DZ6PSN7RJX6R"
heroku config:set AWS_SECRET_ACCESS_KEY="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"
heroku config:set AWS_DEFAULT_REGION="ap-northeast-1"
heroku config:set AWS_BUCKET="quark2galaxy-bbs"
```

## 17. 最新の環境変数確認
```
heroku config
```

>=== quark2galaxy-laravel-bbs Config Vars
>APP_KEY:               base64:Nl9uPLdkE20+6OLEYT80mEvLlHehUlkqMaM3lHhmPkA=<br>
>AWS_ACCESS_KEY_ID:     AKIASNU7DZ6PSN7RJX6R<br>
>AWS_BUCKET:            quark2galaxy-bbs<br>
>AWS_DEFAULT_REGION:    ap-northeast-1<br>
>AWS_SECRET_ACCESS_KEY: tAPdXiJZgjZIjM9cDJVWArX+D5EtUQrn5xy5996Z<br>
>DATABASE_URL:          postgres://ygtjmxlenghsoc:32d35519690b4a91f35fffda64c8b3748bdda8dd8153fe04da013b5483ad1356@ec2-52-72-125-94.compute-1.amazonaws.com:5432/dctg5apf3kva5c<br>
>DB_CONNECTION:         pgsql<br>
>DB_DATABASE:           dctg5apf3kva5c<br>
>DB_HOST:               ec2-52-72-125-94.compute-1.amazonaws.com<br>
>DB_PASSWORD:           32d35519690b4a91f35fffda64c8b3748bdda8dd8153fe04da013b5483ad1356<br>
>DB_USERNAME:           ygtjmxlenghsoc

## 18. Herokuアプリの起動
```
https://quark2galaxy-laravel-bbs.herokuapp.com/
```


