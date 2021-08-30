# Laravelアプリ作成
<p style='text-align: right;'> &copy; 20210824 by Takanori Shima </p>

```
* 以下、Cloud9上でターミナルを起動してコマンドを打つ *
```
## 1. Composerを使って、5.8バージョンのLaravelプロジェクト（bbs）を新規作成
```
composer create-project --prefer-dist laravel/laravel bbs "5.8.*"
```

## 2. /bbs/app/Http/Middleware/TrustProxies.php 変更
* Cloud9の環境ではAWSのロードバランサが使用されているため、LaravelがURLをうまく生成できない問題あり 
```
protected $proxies = '**'; // 全プロキシを信用
```

## 3. Git初期設定
```
git config --global user.name "TakanoriShima"
git config --global user.email "quark2galaxy@gmail.com"
git config --global -l
```
> credential.helper=!aws codecommit credential-helper $@<br>
> credential.usehttppath=true<br>
> core.editor=nano<br>
> user.name=TakanoriShima<br>
> user.email=quark2galaxy@gmail.com<br>

```
cd ~/environment/bbs
git init
git add .
git commit -m "プロジェクト作成とプロキシ設定"
git log
```

## 4. Githubのリポジトリの作成とpush
```
git remote add origin https://github.com/TakanoriShima/bbs_laravel_5.8.git
git branch -M main
git push -u origin main
```

### 以下のGithubリポジトリに履歴が残っていることを確認
https://github.com/TakanoriShima/bbs_laravel_5.8

## 5. キャッシュクリア
```
php artisan cache:clear
```

## 6. Laravel サーバ起動と初期画面のプレビュー
```
pkill php
php artisan serve --host=$IP --port=$PORT
```

### 7. Laravelサーバを落とす
```
コントロール　+ C
```

