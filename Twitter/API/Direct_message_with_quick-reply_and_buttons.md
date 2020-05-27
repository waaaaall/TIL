備忘録。

# やってみる
詳しいドキュメントは[ここ](https://developer.twitter.com/en/docs/direct-messages/sending-and-receiving/api-reference/new-event)を参照。

Twitterの開発者申請は済んでいるものとして進めていきます。
今回もPHPを使用します。

## テキストのみのメッセージ
`$composer require abraham/twitteroauth`
まずはAPIを叩くためにcomposerをで`TwitterOAuth`のライブラリをインストールしましょう。
APIキーやトークンを設定するだけでAPIを叩いてくれるすごいやつです。

```php:send_message.php
<?php
require './vendor/autoload.php';

use Abraham\TwitterOAuth\TwitterOAuth;
use Dotenv\Dotenv;

$dotenv = Dotenv::create($_SERVER['PWD']);
$dotenv->load();

$twitter = new TwitterOAuth(
    $_SERVER['CONSUMER_KEY'],
    $_SERVER['CONSUMER_SECRET_KEY'],
    $_SERVER['OAUTH'],
    $_SERVER['OAUTH_SECRET']
);

// 送信先のTwitterアカウントのID
$recipient_id = '*****';

// 送信内容
$text = "API経由でダイレクトメッセージを送信したよ";

$params = [
    'event' => [
        'type' => 'message_create',
        'message_create' => [
            'target' => [
                'recipient_id' => $recipient_id
            ],
            'message_data' => [
                "text" => $text
            ]
        ]
    ]
];

$res = $twitter->post('direct_messages/events/new', $params, true);
var_dump($res);
```
APIキー，トークンは`vlucas/phpdotenv`を使用しているため`$_SERVER`から読み込んでいますがベタ打ちで構いません。

`$recipient_id`にはダイレクトメッセージの送信先のIDを入れます。
もしIDがわからないという場合は[idtwi](https://idtwi.com/)などのサービスを利用してください。
`$text`には送信したい内容を入れておきます。

上記をまとめて`$params`として`$twitter->post()`の第2引数に渡します。
エラーを吐かなければ成功です。
### 結果
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/210932/93f98f40-866f-a76c-eb3f-7ff882cbbb28.png)  
このようにメッセージが送られてきます。

## ボタン付きのメッセージ
次はボタン付きのメッセージを送信します。
ドキュメントは[こちら](https://developer.twitter.com/en/docs/direct-messages/buttons/overview)

```php:send_message.php
<?php
require './vendor/autoload.php';

use Abraham\TwitterOAuth\TwitterOAuth;
use Dotenv\Dotenv;

$dotenv = Dotenv::create($_SERVER['PWD']);
$dotenv->load();

$twitter = new TwitterOAuth(
    $_SERVER['CONSUMER_KEY'],
    $_SERVER['CONSUMER_SECRET_KEY'],
    $_SERVER['OAUTH'],
    $_SERVER['OAUTH_SECRET']
);

// 送信先
$recipient_id = '*****';

//送信内容
$text = 'API経由でボタン付きのメッセージを送信したよ';
$ctas = [
    [
        "type" => "web_url",
        "label" => "Google",
        "url" => "https://www.google.com/"
    ], [
        "type" => "web_url",
        "label" => "Apple",
        "url" => "https://www.apple.com"
    ], [
        "type" => "web_url",
        "label" => "Microsoft",
        "url" => "https://www.microsoft.com"
    ]
];

$params = [
    'event' => [
        'type' => 'message_create',
        'message_create' => [
            'target' => [
                'recipient_id' => $recipient_id
            ],
            'message_data' => [
                "text" => $text,
                "ctas" => $ctas,
            ]
        ]
    ]
];

$res = $twitter->post('direct_messages/events/new', $params, true);
var_dump($res);
```

基本は同じです。
`$params`に`ctas`を追加しました。
`type`,`label`,`url`の3つを設定します。
1メッセージにボタンは最大3つまで付与することが可能です。
### 結果
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/210932/837e9f13-2a93-3f91-f409-e0865967b797.png)  
メッセージの下にボタンが付与されました。

## クイックリプライ付きのメッセージ
次はクイックリプライ付きのメッセージです。
ドキュメントは[こちら](https://developer.twitter.com/en/docs/direct-messages/quick-replies/overview)

```php:send_message.php
<?php
require './vendor/autoload.php';

use Abraham\TwitterOAuth\TwitterOAuth;
use Dotenv\Dotenv;

$dotenv = Dotenv::create($_SERVER['PWD']);
$dotenv->load();

$twitter = new TwitterOAuth(
    $_SERVER['CONSUMER_KEY'],
    $_SERVER['CONSUMER_SECRET_KEY'],
    $_SERVER['OAUTH'],
    $_SERVER['OAUTH_SECRET']
);

// 送信先
$recipient_id = '*****';

//送信内容
$text = 'API経由でクイックリプライ付きのメッセージを送信したよ';
$quick_reply = [
    "type" => "options",
    "options" => [
        [
            "label" => "Yes",
            "description" => "Yesと送信するよ",
        ], [
            "label" => "No",
            "description" => "Noと送信するよ",
        ], [
            "label" => "ダイレクトメッセージ",
            "description" => 'direct_message',
        ], [
            "label" => "クイックリプライ",
            "description" => "quick_reply",
        ],
    ]
];

$params = [
    'event' => [
        'type' => 'message_create',
        'message_create' => [
            'target' => [
                'recipient_id' => $recipient_id
            ],
            'message_data' => [
                "text" => $text,
                "quick_reply" => $quick_reply
            ]
        ]
    ]
];

$res = $twitter->post('direct_messages/events/new', $params, true);
var_dump($res);
```
`$params`に`quick_reply`を追加しました。
`label`,`description`の2つを設定します。
`description`は必須ではありませんが，設定する場合には全てに設定する必要があるようです。
1つだけ設定することはできませんでした。
クイックリプライは最大4つまで付与させることが可能です。

### 結果
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/210932/9d39d560-cc6f-9a16-8fe5-872cbcd9d1e7.png)
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/210932/f4a3aef7-d7f5-4a47-577e-1865955ef99f.png)  
メッセージ欄の下にクイックリプライが現れました。
クイックリプライの選択肢は1度送信すると消えてしまいます。

## ボタンとクイックリプライが付いたメッセージを送る
ボタンとクイックリプライは同時に付与させることも可能です。

```php:send_message.php
<?php
require './vendor/autoload.php';

use Abraham\TwitterOAuth\TwitterOAuth;
use Dotenv\Dotenv;

$dotenv = Dotenv::create($_SERVER['PWD']);
$dotenv->load();

$twitter = new TwitterOAuth(
    $_SERVER['CONSUMER_KEY'],
    $_SERVER['CONSUMER_SECRET_KEY'],
    $_SERVER['OAUTH'],
    $_SERVER['OAUTH_SECRET']
);

// 送信先
$recipient_id = '*****';

//送信内容
$text = 'API経由でてんこもりのメッセージを送信したよ';
$ctas = [
    [
        "type" => "web_url",
        "label" => "Google",
        "url" => "https://www.google.com/"
    ], [
        "type" => "web_url",
        "label" => "Apple",
        "url" => "https://www.apple.com"
    ], [
        "type" => "web_url",
        "label" => "Microsoft",
        "url" => "https://www.microsoft.com"
    ]
];
$quick_reply = [
    "type" => "options",
    "options" => [
        [
            "label" => "Yes",
            "description" => "Yesと送信するよ",
        ], [
            "label" => "No",
            "description" => "Noと送信するよ",
        ], [
            "label" => "ダイレクトメッセージ",
            "description" => 'direct_message',
        ], [
            "label" => "クイックリプライ",
            "description" => "quick_reply",
        ],
    ]
];

$params = [
    'event' => [
        'type' => 'message_create',
        'message_create' => [
            'target' => [
                'recipient_id' => $recipient_id
            ],
            'message_data' => [
                "text" => $text,
                "ctas" => $ctas,
                "quick_reply" => $quick_reply
            ]
        ]
    ]
];

$res = $twitter->post('direct_messages/events/new', $params, true);
var_dump($res);
```
先程の2つを組み合わせたものになります。

### 結果
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/210932/2f44eb23-40b0-e613-9a4f-c3a0eb9d5113.png)  
キメラみたいなメッセージが届きました。

## まとめ
Twitter APIを使ってダイレクトメッセージを送ってみました。
ボタンやクイックリプライを使うことでテキストのみではなく情報量の多いメッセージを送れます。
どうにかして有効活用したい…
