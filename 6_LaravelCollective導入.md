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
## 3. Laravel Collective を用いてビューを変形
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
第3引数：渡したデータを配列形式で指定（今回は不要なので空っぽの配列 []）
第4引数：HTML タグの属性を配列形式で指定（今回は Bootstrap のボタンとして表示するためのクラスを指定）
```

### /bbs/resources/views/top.blade.php 変形
```
@extends('layouts.app')
@section('title', '会員制写真投稿サイト')
@section('content')
    <div class="row">
        <p>{{ Auth::user()->name }}さん、ようこそ！</p>
        {!! link_to_route('logout.get', 'ログアウト', [], ['class' => 'offset-sm-1 col-sm-4 btn btn-danger']) !!}
    </div>
@endsection
```

### /bbs/resources/views/auth/login.blade.php 変形
```
@extends('layouts.app')
@section('title', 'ログイン')
@section('content')
    <div class="text-center">
        <h1>ログイン</h1>
    </div>

    <div class="row">
        <div class="col-sm-6 offset-sm-3">

            {!! Form::open(['route' => 'login.post']) !!}
                <div class="form-group">
                    {!! Form::label('email', 'メールアドレス') !!}
                    {!! Form::email('email', old('email'), ['class' => 'form-control']) !!}
                </div>

                <div class="form-group">
                    {!! Form::label('password', 'パスワード') !!}
                    {!! Form::password('password', ['class' => 'form-control']) !!}
                </div>

                {!! Form::submit('ログイン', ['class' => 'btn btn-primary btn-block']) !!}
            {!! Form::close() !!}
        </div>
    </div>
@endsection
```

### /bbs/resources/views/auth/register.blade.php 変形
```
@extends('layouts.app')
@section('title', '新規会員登録')
@section('content')
    <div class="text-center">
        <h1>新規会員登録</h1>
    </div>

    <div class="row">
        <div class="col-sm-6 offset-sm-3">

            {!! Form::open(['route' => 'signup.post']) !!}
                <div class="form-group">
                    {!! Form::label('name', '名前') !!}
                    {!! Form::text('name', old('name'), ['class' => 'form-control']) !!}
                </div>

                <div class="form-group">
                    {!! Form::label('email', 'メールアドレス') !!}
                    {!! Form::email('email', old('email'), ['class' => 'form-control']) !!}
                </div>

                <div class="form-group">
                    {!! Form::label('password', 'パスワード') !!}
                    {!! Form::password('password', ['class' => 'form-control']) !!}
                </div>

                <div class="form-group">
                    {!! Form::label('password_confirmation', 'パスワードの確認') !!}
                    {!! Form::password('password_confirmation', ['class' => 'form-control']) !!}
                </div>

                {!! Form::submit('登録', ['class' => 'btn btn-primary btn-block']) !!}
            {!! Form::close() !!}
        </div>
    </div>
@endsection
```

## 4. Git
```
git add .
git commit -m "Laravel Collective 導入とビューファイルの表記変更"
git push origin main
```
