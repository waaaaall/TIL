Twitterにはウェルカムメッセージを設定することが可能です。
日本語のドキュメントがあまり見当たらなかったので備忘録。

追記 2020/05/28
なんかRMT業者がチャットボットに利用しているようでメッセージ設定するだけで金とってるみたいですね？？？？それの影響でタダで設定したい一般の方が流れてきてるぽい？？？？？？

実装されたものがどう使われようが気にしませんが、あくまでもこの記事は私自身の備忘録であり、0からのハンズオンで記事を書いているわけではないです。
冒頭の流れでこの記事に辿り着いた非技術者の方は、実装するには最低限の知識が必要であることを理解してください。
5/28にQiitaからTILへ移行

追記 2020/06/02
技術者以外のコメントが増えたためQiitaを削除
忘れかけてた頃にコメントされた勢いでカッとなって消してしまった
話のネタになるから残しておけばよかったかもなあ、、、ミスった

# ウェルカムメッセージ
初めてダイレクトメッセージを送る（または会話を始める）ときに自動で表示されます。

ウェルカムメッセージを表示するには2つの方法が存在します。
- ダイレクトメッセージ画面を開いた場合にデフォルトで表示されるメッセージ
- ツイートに付与したボタン（ディープリンク）から遷移する場合にのみ表示される特定のメッセージ

# 設定してみる
詳しいドキュメントは[Welcome Messages](https://developer.twitter.com/en/docs/direct-messages/welcome-messages/overview)を参照。

Twitterの開発者申請は済んでいるものとして進めていきます。
今回もPHPを使用します。

## TwitterOAuth
Twitter APIを使用するために`abraham/twitteroauth`を使用します。

`$composer require abraham/twitteroauth`
Composerでインストールします。

## メッセージを登録する
ウェルカムメッセージは表示させるためにはアカウントにメッセージを登録する必要があります。

ドキュメントは下記
[direct_messages/welcome_messages/new](https://developer.twitter.com/en/docs/direct-messages/welcome-messages/api-reference/new-welcome-message)

```php:create_message.php
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

$text = "これはWelcomeMessageです。
自動で送信されます。";

$params = [
    "welcome_message" => [
        "message_data" => [
            "text" => $text
        ]
    ]
];

$res = $twitter->post('direct_messages/welcome_messages/new', $params, true);
var_dump($res);

```

ウェルカムメッセージの基本はダイレクトメッセージと同じです。
`message_data`にメッセージで表示させたいテキストを記述します。

リクエストが成功すると下記のレスポンスが返ってきます。

```json
{
  "welcome_message": {
    "id": "1181826364040605700",
    "created_timestamp": "1570593298193",
    "message_data": {
      "text": "これはWelcomeMessageです。
自動で送信されます。",
      "entities": {
        "hashtags": [],
        "symbols": [],
        "user_mentions": [],
        "urls": []
      }
    },
    "source_app_id": "*****"
  },
  "apps": {
    "*****": {
      "id": "*****",
      "name": "AppsName",
      "url": "https:\\/\\/twitter.com\\/good_wall"
    }
  }
}
```
重要なのは`welcome_message`内の`id`です。
デフォルトで表示する場合にもディープリンクから表示する場合にもこの`id`が必要なためメモ等しておきます。
忘れたとしても[GET direct_messages/welcome_messages/list](https://developer.twitter.com/en/docs/direct-messages/welcome-messages/api-reference/list-welcome-messages)で登録しているメッセージの一覧を取得することができます。

メッセージは1つだけではなく複数登録することが可能です。

## デフォルトで表示する
メッセージは登録しただけでは表示されることはありません。
まずはデフォルトで表示されるように設定してみます。

ドキュメントは下記
[POST direct_messages/welcome_messages/rules/new](https://developer.twitter.com/en/docs/direct-messages/welcome-messages/api-reference/new-welcome-message-rule)

```php:create/rules.php
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

$welcome_message_id = "1181826364040605700";

$params = [
    "welcome_message_rule" =>
    [
        "welcome_message_id" => $welcome_message_id
    ]
];

$res = $twitter->post('direct_messages/welcome_messages/rules/new', $params, true);
var_dump($res);
```

`$welcome_message_id`には先程メッセージの登録時に返ってきた`id`を記述します。

リクエストが成功すると下記のレスポンスが返ってきます。

```json
{
  "welcome_message_rule": {
    "id": "1181817806356172805",
    "created_timestamp": "1570601570303",
    "welcome_message_id": "1181826364040605700"
  }
}
```
指定した`welcome_message_id`が含まれた`welcome_message_rule`が返ってくれば成功です。

実際の挙動を確認します。  
![ezgif-3-5d981add13c6.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/210932/01cc923e-7c8e-579b-20d7-fe56fcee88e4.gif)
DM画面を開くと自動でメッセージが表示されました。(ブラウザだとラグが…)

デフォルトで設定できるメッセージは1アカウントに対して1つです。
メッセージを変更をする場合には現在設定されているルールを削除してから新しいルール登録する必要があります。

## 特定のツイートから表示する
ディープリンクを使用することでデフォルト以外のメッセージを表示させることも可能です。

ドキュメントは下記
[Deeplinking to a Welcome Message](https://developer.twitter.com/en/docs/direct-messages/welcome-messages/guides/deeplinking-to-welcome-message)

> Example Direct Message Deeplink: 
https://twitter.com/messages/compose?recipient_id=3805104374&welcome_message_id=12345

ディープリンクはTwitterのuser_idとwelcome_message_idを用いて作成します。
`recipient_id`にはusr_idを，`welcome_message_id`にはwelcome_message_idを指定します。

新たにディープリンク用のメッセージを登録しました。

```json
{
  "welcome_message": {
    "id": "1181846303585771525",
    "created_timestamp": "1570604597384",
    "message_data": {
      "text": "これはディープリンクで表示されるメッセージです。",
      "entities": {
        "hashtags": [],
        "symbols": [],
        "user_mentions": [],
        "urls": []
      }
    },
    "source_app_id": "*****"
  },
  "apps": {
    "*****": {
      "id": "*****",
      "name": "AppsName",
      "url": "https:\\/\\/twitter.com\\/good_wall"
    }
  }
}
```

これを元にディープリンクを作成します。

> https://twitter.com/messages/compose?recipient_id=127167709&welcome_message_id=1181846303585771525
> ※クリックするとTwitterのDM画面が開いてメッセージが飛んできます。

このディープリンクを呟くとダイレクトメッセージボタンが生成されます。
![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/210932/1b1f5997-7be8-1731-ccf9-49716cc4a7f4.png)
上記リンク及びボタンからDM画面を開くと先程登録したメッセージが表示されます。

ディープリンクからDM画面に遷移した場合はデフォルトのメッセージは表示されません。

## 他
基本がダイレクトメッセージなのでボタンやクイックリプライの設定が可能です。
![ezgif-3-0e8390b31192.gif](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/210932/92fea9e5-db26-b779-f55f-e2f404e32fea.gif)

ボタンやクイックリプライについては[こちら](https://qiita.com/waaaaall/items/64128c914df754a41c1f)を参考にしてください。

# まとめ
Account Activity APIと併用すればチャットボット開発できるな〜とか思いました。

デフォルトのメッセージは停止済みですが，ディープリンクの方は残してあります。
実際に試してみたい方はリンクを押してみてください。
ウェルカムメッセージの履歴は送信者側には残りません。
そのため通知等がこちらに飛ぶことはないので安心してください。

おわり。
