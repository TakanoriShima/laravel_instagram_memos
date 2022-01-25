# UsersControllerの作成と会員一覧・詳細表示
<p style='text-align: right;'> &copy; 20210824 by Takanori Shima </p>

```
* 以下、Cloud9上でターミナルを起動してinstagramフォルダ内でコマンドを打つ *
```
## 1. UsersController作成
```
php artisan make:controller UsersController --resource --model=User
```

## 2. /instagram/app/Http/Controllers/UsersController.php の編集
### index, showアクションだけ残して他のアクションは削除
```
<?php

namespace App\Http\Controllers;

use App\User;
use Illuminate\Http\Request;

class UsersController extends Controller
{
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        //
    }

    /**
     * Display the specified resource.
     *
     * @param  \App\User  $user
     * @return \Illuminate\Http\Response
     */
    public function show(User $user)
    {
        //
    }
}

```
## 3. /instagram/routes/web.php 追記
```
// 一般ユーザー
Route::group(['middleware' => ['guest']], function () {
    
    // プレビューした瞬間の設定
    Route::get('/', 'ToppagesController@index');
    // ログイン認証系
    Route::get('login', 'Auth\LoginController@showLoginForm')->name('login');
    Route::post('login', 'Auth\LoginController@login')->name('login.post');
    // ユーザ登録系
    Route::get('signup', 'Auth\RegisterController@showRegistrationForm')->name('signup.get');
    Route::post('signup', 'Auth\RegisterController@register')->name('signup.post');

});

// ユーザー認証必要
Route::group(['middleware' => ['auth']], function () {
    
    // ログイン後のリダイレクト先
    Route::get('top', function () {
         return view('top');
    });
    
    // ログアウト
    Route::get('logout', 'Auth\LoginController@logout')->name('logout.get');
    
    // ユーザー一覧、詳細表示
    Route::resource('users', 'UsersController', ['only' => ['index', 'show']]);

});

```

## 4. 最新ルーティング確認
```
php artisan route:list

+--------+----------+--------------+-------------+-------------------------------------------------------------------+--------------+
| Domain | Method   | URI          | Name        | Action                                                            | Middleware   |
+--------+----------+--------------+-------------+-------------------------------------------------------------------+--------------+
|        | GET|HEAD | /            |             | App\Http\Controllers\ToppagesController@index                     | web,guest    |
|        | GET|HEAD | api/user     |             | Closure                                                           | api,auth:api |
|        | GET|HEAD | login        | login       | App\Http\Controllers\Auth\LoginController@showLoginForm           | web,guest    |
|        | POST     | login        | login.post  | App\Http\Controllers\Auth\LoginController@login                   | web,guest    |
|        | GET|HEAD | logout       | logout.get  | App\Http\Controllers\Auth\LoginController@logout                  | web,auth     |
|        | GET|HEAD | signup       | signup.get  | App\Http\Controllers\Auth\RegisterController@showRegistrationForm | web,guest    |
|        | POST     | signup       | signup.post | App\Http\Controllers\Auth\RegisterController@register             | web,guest    |
|        | GET|HEAD | top          |             | Closure                                                           | web,auth     |
|        | GET|HEAD | users        | users.index | App\Http\Controllers\UsersController@index                        | web,auth     |
|        | GET|HEAD | users/{user} | users.show  | App\Http\Controllers\UsersController@show                         | web,auth     |
+--------+----------+--------------+-------------+-------------------------------------------------------------------+--------------+
```

## 5. /instagram/app/Http/Controllers/UsersController.php 編集
``` 
    /**
     * Display a listing of the resource.
     *
     * @return \Illuminate\Http\Response
     */
    public function index()
    {
        // Userモデルを使って、全ユーザーデータを取得
        $users = User::all();
        // viewの呼び出し
        return view('users.index', compact('users'));
    }

    /**
     * Display the specified resource.
     *
     * @param  \App\User  $user
     * @return \Illuminate\Http\Response
     */
    public function show(User $user)
    {
        // view の呼び出し
        return view('users.show', compact('user'));
    }
```

## 6. /instagram/resources/views/users/index.blade.php 新規作成・編集
```
@extends('layouts.app')
@section('title', '会員一覧')
@section('content')
    <div class="text-center">
        <h1>会員一覧</h1>
    </div>
    <div class="row">
        <table class="table table-bordered table-striped">
            <tr>
                <th>ID</th>
                <th>名前</th>
                <th>メールアドレス</th>
                <th>登録日時</th>
            </tr>
            @foreach($users as $user)
            <tr>
                <td>{!! link_to_route('users.show', $user->id , ['id' => $user->id ],[]) !!}</td>
                <td>{{ $user->name }}</td>
                <td>{{ $user->email }}</td>
                <td>{{ $user->created_at }}</td>
            </tr>
            @endforeach
        </table>
    </div>
@endsection
```


## 7. /instagram/resources/views/users/show.blade.php 新規作成・編集
```
@extends('layouts.app')
@section('title', $user->name . 'さんのマイページ')
@section('content')
    <div class="text-center">
        <h1>{{ $user->name }} さんのマイページ</h1>
    </div>
@endsection
```

## 8. /instagram/resources/views/layouts/app.blade.php 変形
```
<!DOCTYPE html>
<html lang="ja">
    <head>
        <meta charset="utf-8">
        <title> @yield('title')</title>
        <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/css/bootstrap.min.css">
        <link rel="icon" href="{{ asset('images/favicon.ico')}} ">
        <link rel="stylesheet" href="{{ asset('css/style.css') }}">
    </head>

    <body>
        <header class="mb-4">
            <nav class="navbar navbar-expand-sm navbar-dark bg-info">
                @if(Auth::check())
                <a class="navbar-brand" href="/top">会員制写真投稿サイト</a>
                @else
                <a class="navbar-brand" href="/">会員制写真投稿サイト</a>
                @endif
                <button type="button" class="navbar-toggler" data-toggle="collapse" data-target="#nav-bar">
                    <span class="navbar-toggler-icon"></span>
                </button>
                
                <div class="collapse navbar-collapse" id="nav-bar">
                    <ul class="navbar-nav mr-auto"></ul>
                    <ul class="navbar-nav">
                        @if(Auth::check())
                        <li>{!! link_to_route('users.index', '会員一覧', [],['class' => 'nav-link']) !!}</li>
                        <li>{!! link_to_route('logout.get', 'ログアウト', [],['class' => 'nav-link']) !!}</li>
                        @endif
                    </ul>
                </div>
            </nav>
        </header>
        
        <div class="container">
            @include('commons.flash_message')
            @include('commons.error_messages')
            @yield('content')
        </div>
        
        <script src="https://code.jquery.com/jquery-3.3.1.slim.min.js"></script>
        <script src="https://cdnjs.cloudflare.com/ajax/libs/popper.js/1.14.6/umd/popper.min.js"></script>
        <script src="https://stackpath.bootstrapcdn.com/bootstrap/4.2.1/js/bootstrap.min.js"></script>
        <script defer src="https://use.fontawesome.com/releases/v5.7.2/js/all.js"></script>
        <script src="{{ asset('js/script.js') }}"></script>
    </body>
</html>
```

## 9. /instagram/resources/views/top.blade.php 変形
```
@extends('layouts.app')
@section('title',  '投稿一覧')
@section('content')
    <div class="text-center">
        <h1>投稿一覧</h1>
    </div>
@endsection
```

## 10. /instagram/public/css/style.css 追記
```
@charset "utf-8";
h1 {
    color: blue;
}
/*media query*/
/*ref)*/
/*https://www.seojuku.com/blog/responsive-mediaquery.html*/

/*タブレット 横は  パソコンと同じ*/
/*タブレット 縦のみ */
@media screen and (max-width: 1024px) and (orientation: portrait){
    
}
/*タブレット 縦から */
@media screen and (max-width: 1024px){
    
}
/*スマホ 横のみ   */
@media screen and (max-width: 896px) and (orientation: landscape){
    
}
/*スマホ 横から   */ 
@media screen and (max-width: 896px){
    
}
/*スマホ　縦からは */
@media screen and (max-width: 480px){
    
}
```

## 11. Git
```
git add .
git commit -m "ユーザー一覧、詳細作成"
git push origin main
```


