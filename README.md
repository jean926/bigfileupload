# 大容量ファイルアップロード (PHP/Apacheの場合)
ギガバイト級の大容量ファイルをアップロードする際の設定について説明します。

## Apacheの設定 ##
httpd.conf または .htaccess に下記を追加
    LimitRequestBody 0
デフォルトでは0だが、何らか制限がある場合には制限解除。

## phpの設定 ##
**php.iniの場合**
    file_uploads = On
    upload_max_filesize = 2G
    post_max_size = 2G
    max_input_time = 60
    memory_limit = 128M
    max_execution_time = 60

**.htaccessの場合**
    php_value file_uploads On
    php_value upload_max_filesize 2G
    php_value post_max_size 2G
    php_value memory_limit 128M
    php_value max_execution_time 60
    php_value max_input_time 60

**php内の場合
    <?php
    ini_set('memory_limit', '40M');
    ini_set('post_max_size', '32M');
    ini_set('upload_max_filesize', '32M');

upload_max_filesize はファイル自体のサイズで、post_max_sizeはPOSTデータとして送られるデータサイズ制限。
POSTデータになると、ファイル情報などのオーバーヘッドがあるので、upload_max_filesizeより大きくする必要がありますが、あまり差が無いので同一の値としています。

memory_limit はファイルサイズより小さくて構いません。 Linuxの場合ファイルはデフォルトで /tmpに保存され、メモリに一時保存されることはありません。実際アップロード中に /tmpディレクトリを監視していると、アップロード中で増加していくファイルが見られます。PHP公式ドキュメントで書かれている、
    一般的に memory_limit は、 post_max_sizeよりも大きく する必要があります。
とは、ファイルアップロード以外の点について言及されているのかもしれません。

max_input_time, max_execution_time どちらもアップロード時間には影響ありません。input_timeはPOSTやGETの入力をパースする最大の時間、execution_timeはスクリプトの実行時間で、問題があれば下記のようなエラーが出るはずです。
    PHP Fatal error:  Maximum execution time of 4 seconds exceeded in Unknown on line 0,

