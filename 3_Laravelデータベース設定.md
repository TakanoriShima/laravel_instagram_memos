# Laravelデータベース設定(MySQL)
<p style='text-align: right;'> &copy; 20210824 by Takanori Shima </p>

```
* 以下、Cloud9上でターミナルを起動してbbsフォルダ内でコマンドを打つ *
```
## 1. MySQLに新規データベース作成
```
sudo service mysqld start
mysql -u root
mysql> create database bbs default character set utf8;
mysql> show databases;
mysql> exit;
```

## 2. Laravelアプリで使用するデータベース情報を変更
### /bbs/.env の編集
```
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=bbs
DB_USERNAME=root
DB_PASSWORD=
```
## 3. tinker で MySQLとの接続確認
```
php artisan tinker
>>> print_r($_ENV);
```
> [DB_CONNECTION] => mysql

```
>>> DB::reconnect();
```
> Illuminate\Database\MySqlConnection {#3234}

```
>>> exit
```
> Exit: Goodbye

## 4. タイムゾーン設定
### /bbs/config/app.php 編集
    'timezone' => 'Asia/Tokyo',
    
## 5. テーブル設計前の初期設定
### /bbs/app/Providers/AppServiceProvider.php 編集
    public function boot()
    {
        \Schema::defaultStringLength(191);
        \URL::forceScheme('https');
    }
    
## 6. Git
```
git add .
git commit -m "MySQL用データベース設定完了"
git push origin main
```