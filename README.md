# 大容量ファイルアップロード (PHP/Apacheの場合)
ギガバイト級の大容量ファイルをアップロードする際の設定について説明します。

## Apacheの設定 ##
httpd.conf または .htaccess に下記を追加

    LimitRequestBody 0
デフォルトでは0で、無制限にアップロード可能。

## phpの設定 ##
**php.iniの場合**

    upload_max_filesize = 2G
    post_max_size = 2G
    memory_limit = 128M
    max_execution_time = 60

**.htaccessの場合**

    php_value upload_max_filesize 2G
    php_value post_max_size 2G
    php_value memory_limit 128M
    php_value max_execution_time 60

*upload_max_filesize* はファイル自体のサイズで、post_max_sizeはPOSTデータとして送られるデータサイズ制限。
POSTデータになると、ファイル情報などのオーバーヘッドがあるので、upload_max_filesizeより大きくする必要がありますが、あまり差が無いので同一の値としています。

*memory_limit* はファイルサイズより小さくて構いません。 Linuxの場合ファイルはデフォルトで /tmpに保存され、メモリに一時保存されることはありません。実際アップロード中に /tmpディレクトリを監視していると、アップロード中で増加していくファイルが見られます。PHP公式ドキュメントで書かれている、

> 一般的に memory_limit は、 post_max_sizeよりも大きく する必要があります。

とは、ファイルアップロード以外の点について言及されているのかもしれません。

*max_input_time, max_execution_time* どちらもアップロード時間には影響ありません。max_input_time(デフォルト:-1)はPOSTやGETの入力をパースする最大の時間で、max_execution_time(デフォルト:30)はスクリプトの実行時間です。

問題があれば下記のようなエラーが出るはずです。

    PHP Fatal error:  Maximum execution time of 4 seconds exceeded in Unknown on line 0,
