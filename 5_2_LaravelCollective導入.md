# Laravel Collective 導入
<p style='text-align: right;'> &copy; 20210824 by Takanori Shima </p>

```
* 以下、Cloud9上でターミナルを起動してbbsフォルダ内でコマンドを打つ *
```
## 1. メモリの開放
```
sudo sh -c "echo 3 > /proc/sys/vm/drop_caches"
```

## 2. Laravel Collective のインストール
```
composer require "laravelcollective/html":"5.8.*"
```

## 3. 会員登録ログイン系のルーティング記述
### /bbs/routes/web.php 追記

```
// プレビューをした瞬間の設定
Route::get('/', 'ToppagesController@index');

// 一般ユーザー
Route::group(['middleware' => ['guest']], function () {
 
    // ユーザ登録系
    Route::get('signup', 'Auth\RegisterController@showRegistrationForm')->name('signup.get');
    Route::post('signup', 'Auth\RegisterController@register')->name('signup.post');
    
    // ログイン認証系
    Route::get('login', 'Auth\LoginController@showLoginForm')->name('login');
    Route::post('login', 'Auth\LoginController@login')->name('login.post');

});


// ユーザー認証必要
Route::group(['middleware' => ['auth']], function () {
    
    // ログアウト処理
    Route::get('logout', 'Auth\LoginController@logout')->name('logout.get');

});
```
## 4. Laravel Collective を用いてビューを変形
### /bbs/resources/views/welcome.blade.php を Laravel Collective 書式を用いて変形

```
@extends('layouts.app')
@section('title', '会員制写真投稿サイト')
@section('content')
    <div class="row">
        {!! link_to_route('signup.get', '新規会員登録', [], ['class' => 'offset-sm-1 col-sm-4 btn btn-primary']) !!}
        {!! link_to_route('login', 'ログイン', [], ['class' => 'offset-sm-1 col-sm-4 btn btn-danger']) !!}
    </div>
@endsection
```

## link_to_route(第1引数, 第2引数, 第3引数, 第4引数)書式に関するコメント
```
第1引数：ルーティング名(web.phpのnameで指定した文字列)
第2引数：リンクにしたい文字列
第3引数：/messages/{message} の {message} のようなURL内のパラメータに代入したい値を配列形式で指定（今回は不要なので空っぽの配列 []）
第4引数：HTML タグの属性を配列形式で指定（今回は Bootstrap のボタンとして表示するためのクラスを指定）
```

## Git
git add .
git commit -m "Laravel Collective 導入とTOP画面の表記変更"
git push origin main
