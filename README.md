# auth-app-practice

## 概要

COACHTECH 教材 Tutorial 10-1「認証機能 ハンズオン演習」で作成した成果物です。
fortifyを用いたユーザー登録およびログイン認証機能があるブラウザページの作成を行いました。

## 使用技術

- PHP 8.x
- Laravel 10.x
- Laravel Fortify（認証）
- MySQL

## 学んだこと

- Fortifyによる登録・ログイン・ログアウトの認証のメソッドを理解できた。また、ルーティングがインストール時に自動的に登録されるためコントローラが不要な点にびっくりした(ただし、ビューの登録は必要)。
- 前回のハンズオンで学んだバリデーションはフォームリクエストではなく、Actionでの設定ができること
- Remember_tokenカラムによるRemember Me機能の有効化やログインユーザーのみホーム（/dashboard）にアクセスできるような仕組み

## 開発の工夫

- 提供bladeファイルにチェックボックスを追加し、Remember Me機能を追加することで誤ってブラウザを消した際に  
  ユーザーダッシュボード( /dashboard )にログイン保持した状態でアクセスできるようにしました。  
  また、ログアウト状態のユーザーについては /dashboard に直接アクセスできないようbladeファイルに、

```
    @else
        <p><a href="{{ route('login') }}">ログイン</a>してください。</p>
```

を追加することで、ログインページ( /login )にリダイレクトするよう設定しました。

- App/Action/CreateNewUser.php 内の Validator::make() の第3変数でバリデーションルールとメッセージを  
  以下のように設定しました。

・App/Action/CreateNewUser.phpのユーザー登録のバリデーションルール

```
        Validator::make($input, [
            'name' => ['required', 'string', 'max:50'],
            'email' => [
                'required',
                'string',
                'email',
                'max:255',
                Rule::unique(User::class),
            ],
            'password' => $this->passwordRules(),
        ], [
            // バリデーションのカスタムメッセージ
            'name.required' => 'お名前を入力してください。',
            'name.max' => '名前は50字以内で入力してください。',
            'email.required' => 'メールアドレスは必須項目です。',
            'email.email' => '正しいメールアドレスの形式で入力してください。',
            'email.unique' => 'このメールアドレスは既に登録されています。',
            'password.required' => 'パスワードを入力してください。',
            'password.min' => 'パスワードは :min 文字以上で入力してください。',
            'password.letters' => 'パスワードには少なくとも1つの文字（アルファベット）を含めてください。',
            'password.numbers' => 'パスワードには少なくとも1つの数字を含めてください。',
            'password.confirmed' => '確認用パスワードと一致しません。',
        ])->validate();
```

・App/Provider/AppServiceProvider.phpにおけるPassword::defaults()の設定

```
    public function boot(): void
    {
        Password::defaults(function () {
            return Password::min(8)
                ->letters()
                ->numbers(); // 8文字以上、アルファベット、数字を必須にする
        });
    }
```

〇バリデーションエラーの一例
<img width="1451" height="1262" alt="Image" src="https://github.com/user-attachments/assets/2ab07204-da40-47ee-99a7-3fda8330c818" />

## 動作確認

http://localhost/register にアクセス

## 動作確認gif

・ユーザーの新規登録・ログイン・ログアウトとログイン保持
<img width="1910" height="1120" alt="Image" src="https://github.com/user-attachments/assets/f764be3e-2e1d-4f43-b51e-bd1075ce2010" />

※ログインしていない状態で/dashボードにアクセスすると/loginにリダイレクトします
<img width="1902" height="1126" alt="Image" src="https://github.com/user-attachments/assets/8ef94c33-ab8c-41c3-8231-f308c81192b4" />
