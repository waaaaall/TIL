# 同一環境で複数のgitアカウントを管理する.md
会社のPCからTILを更新したりちょっとした時間にプライベートプロジェクトを触りたい！  
でも会社gitアカウントからはpushできない！  
というときの話  
  
色々方法はあるらしいのだが一番わかりやすいなと思ったのはgitconfig  
gitconfigは3段階の設定が存在する。  
* システム全体のsystem  
* ユーザ全体のglobal  
* 対象リポジトリのみのlocal  
  
systemはシステムデフォルトなので今回は置いといて，注目するのはglobalとlocal  
globalはユーザー全体の設定になるためデフォルトではここに記述された内容が反映される。  
**localは対象リポジトリ毎に設定することができる**  
  
てことで求めていたのはlocalの設定でした。  
早速設定しましょう。  
対象リポジトリで下記コマンドを入力するだけです。  
`git config --local user.name プライベートアカウント`  
`git config --local user.email プライベートアカウント`  
  
configは`~/.git/config`に存在するので設定が反映されているか確認しましょう。  
`cat /.git/config`  
  
> [user]  
> 	name = プライベートアカウント  
>	email = プライベートアカウント  
  
userの部分に先程入力したものに変更されていれば大丈夫です。  
これで会社にいながらプライベートな作業が可能ですやったね！  
あっ，僕はTILのためにこの作業を行いました。
  
---
SSHキーの設定等は省いているのでやっておいてね。  
