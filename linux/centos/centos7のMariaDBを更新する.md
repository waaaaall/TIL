# CentOS7のMariaDBを更新する
Webサーバ側のDB構築をしていたときにクエリが通らず調べると，DBのバージョンが低いことが原因だった。  
調べるとMySQL5.5だったためアップデートをすることにした。
  
## MariaDB
CentOSには標準でMariaDBが付属しており,これはMySQLの派生として開発されているらしい。  
MySQLだと思っていたものはMariaDBであり，MariaDBを更新する必要があることがわかった。  
MariaDB5.5はMySQL5.5をベースに開発されており，以降のMariaDB10.0(一気に数字が飛ぶ)はMariaDB5.5をベースに開発されているそうだ。  
  
## MariaDBのアップデート
まずはCentOSにMariaDBのyumリポジトリを追加する必要がある。  
`curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash`  
上記のコマンドを実行するだけで1発で必要なリポジトリを設定してくれる。  
  
リポジトリを追加したらMariaDBを更新する。  
MariaDBが動いているようであれば停止させる。  
`systemctl stop mariadb`  
  
次のコマンドで更新を行う。  
`yum update MariaDB-server MariaDB-client`  
  
更新が無事に終了したらMariaDBを開始する。  
`systemctl start mariadb`  
  
上記で更新が完了する。  
バージョンから更新が完了していることを確認しよう。  
  
## 弊社の話
Pleskで管理をしている場合，管理画面にアクセスしようとすると`ERROR: Zend_Db_Adapter_Exception: SQLSTATE[HY000] [2002] No such file or directory
`が表示される場合がある。  
その場合は`systemctl start mysql`を実行した後に`plesk sbin packagemng -sdf`を実行する。  
これでPlesk内のパッケージが更新されるはずだ。
