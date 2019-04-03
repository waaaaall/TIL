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
  
### 参考URL
`https://qiita.com/nyanyamo/items/ccd7141edd85175c0a93`  
`https://mariadb.com/kb/en/library/yum/`  
`https://mariadb.com/kb/en/library/mariadb-package-repository-setup-and-usage/`  
`https://support.plesk.com/hc/ja/articles/213403429--MySQL-%E3%82%92%E3%83%90%E3%83%BC%E3%82%B8%E3%83%A7%E3%83%B3-5-5-%E3%81%8B%E3%82%89-5-6-%E3%81%AB%E3%82%A2%E3%83%83%E3%83%97%E3%82%B0%E3%83%AC%E3%83%BC%E3%83%89%E3%81%99%E3%82%8B%E3%81%AB%E3%81%AF%E3%81%A9%E3%81%86%E3%81%99%E3%82%8C%E3%81%B0%E3%82%88%E3%81%84%E3%81%A7%E3%81%99%E3%81%8B-`  
`https://support.plesk.com/hc/en-us/articles/360002343794-Plesk-inaccessible-ERROR-Zend-Db-Adapter-Exception-SQLSTATE-HY000-2002-No-such-file-or-directory`  
