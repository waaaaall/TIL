# bitbucket上のライブラリをcomposerで読み込む方法  
開発効率向上のために社内ライブラリを作成することになった。  
composerを用いて管理を行うが社内ライブラリになるためpackagistに登録することはできない。  
そのためbitbucketから直接読み込むことになったのでその方法。  
  
とりあえず自作ライブラリをbitbucket上のリポジトリに突っ込む  
ライブラリを読み込みたいプロジェクトのcomposer.jsonに  
  
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
  
---
### 追記20190222  
`"url": "リポジトリurl"`がsshかhttpsかでエラーを吐く場合がある。  
sshの場合はリポジトリのアカウントにssh keyを登録しておく必要がある。  
httpsの場合は`comopsr update`時にuserとpassを求められる。  
urlにuserとpassを含むことでも認証が可能。  
