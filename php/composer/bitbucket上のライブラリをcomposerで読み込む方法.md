# bitbucket上のライブラリをcomposerで読み込む方法
開発効率向上のために社内ライブラリを作成することになった。
composerを用いて管理を行うが社内ライブラリになるためpackagistに登録することはできない。
そのためbitbucketから直接読み込むことになったのでその方法。

とりあえず自作ライブラリをbitbucket上のリポジトリに突っ込む
ライブラリを読み込みたいプロジェクトのcomposer.initに

>{
>    "name": "project_owner/project",
>    "type": "project",
>    "authors": [
>        {
>            "name": "MyName",
>            "email": "exmaple@co.jp"
>        }
>    ],
>    "repositories":[
>      {
>        "packagist": false
>      },
>      {
>        "type": "git",
>        "url": "リポジトリURL"
>      }
>    ],
>    "require": {
>        "自作ライブラリ名": "dev-master"
>    }
>}

こんな感じで記述する。

`packagist: false`を記述するとpacakgistを探しに行かないため多少早くなるらしい。

後は`composer update`をして終わり。
