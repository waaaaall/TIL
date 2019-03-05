#phpdotenvの使い方  
プロジェクトのrootディレクトリに`.env`ファイルを作成  
`.env`ファイルを内に環境変数を記述する。  
>> #例  
>> NAME="name"  

仕様する場合は  
>> $dotenv = Dotenv\Dotenv::create(dirname(__FILE__));  
>> $dotenv->load();  
で読み込むことが出来る。  


`$_ENV['name']`,`$_SERVER['name']`,`getenv('name')`等で呼び出し可能。  

おそらく利用する場合は環境変数がメインのため`.env`ファイルは`.gitignore`でリモートにはあげない。  
`.env.example`などのファイルを作成しテンプレートとして使用すると良い。
