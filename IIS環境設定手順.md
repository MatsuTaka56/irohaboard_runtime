# IIS環境の構築手順
IIS環境でirohaboardを動作させるためのもろもろの設定手順をまとめます。
※私が動作させたときの一例です。

1. ## PHP
    1. ### PHPの設定 ==========  
        1. <https://www.php.net>から「php-8.5.13-nts-Win32-vs17-x64.zip」（その時の最新版）をダウンロード
        1. zip解凍し、解凍してできたフォルダ名を「php」に変更し、「C:直下」に配置

    1. ### xdebugの設定 ==========  
        1.  <https://xdebug.org/download>から「Latest Release」 → 「Windows binaries:」 → 「PHP 8.5 VS17 (64 bit)」をクリックし、「php_xdebug-3.5.3-8.5-nts-vs17-x86_64.dll」（使用するPHPバージョンに合わせて）をダウンロード
        1. 「php_xdebug.dll」に名前を変更し、「C:php/ext」に配置

    1. ### php.iniの設定 ==========  
        1. php内の「php.ini-production」をその場にコピーし、ファイル名を「php.ini」に変更
        1. 「php.ini」の内容を以下の通り変更  
           - 'extension'群の'fileinfo', 'mbstring', 'mysqli', 'pdo_mysql', 'openssl', 'zip'を'有効'に変更（行頭の';'を削除）
           - 'zend_extension = xdebug'を'extension'群の最後に追加
           - 次のパラメータを'有効'に変更し（行頭の';'を削除）、値を設定  
             'extension_dir = "ext"'  
             'cgi.force_redirect = 0'  
             'cgi.fix_pathinfo=1'
             'fastcgi.impersonate = 1'  
             'fastcgi.logging = 0'  
             'mbstring.language = Japanese'  
             'mbstring.internal_encoding = UTF-8'  
             'mbstring.http_input = auto'  
             'mbstring.http_output = UTF-8'  
             'mbstring.encoding_translation = On'  
             'mbstring.detect_order = auto'

    1. ### VC連携の設定 ==========  
        1. <https://learn.microsoft.com/ja-jp/cpp/windows/latest-supported-vc-redist?view=msvc-170>から「Visual C++ v14 再頒布可能パッケージ」  
             → 「サポートされている最新の再頒布可能パッケージバージョン」  
             → 「X64」  
             →  <https://aka.ms/vc14/vc_redist.x64.exe>をダウンロード
        1. 「VC_redist.x64.exe」を実行（インストール）
        1. 「c:php/php-cgi.exe」を実行  
        → エラーが出なければOK（ターミナルが起動）  
        → PHP設定終わり

1. ## IIS
    1. ### IISの設定 ==========  
        1. 「コントロールパネル」  
        → 「プログラムと機能」  
        → 「Windowsの機能の有効化または無効化」  
        → 「インターネットインフォメーションサービス」チェックオン、展開  
        　→ 「WorldWideWebサービス」チェックオン、展開  
        　　→ 「アプリケーション開発機能」チェックオン、展開  
        　　　→ 「Application Initialization」以外すべてチェックオン
        1. それ以外はデフォルトのまま
        1. 「OK」  
        → 必要なファイルを確認  
        → 「一部の機能のインストールを完了するには、、、、」画面で「Windows Updateでファイルを自動ダウンロードする」を選択  
        → 「必要なファイルをダウンロードしています」画面表示
        1. ダウンロードが完了すると自動的に適用  
        → 完了  
        → 「閉じる」

    1. ### サイトを分ける場合 ==========  
        1. Windows管理ツールから「インターネットインフォメーションサービス(IIS)マネージャ」を起動
        1. 左ペインの「サイト」を右クリックし、「Webサイトの追加」を選択
        1. 次ンパラメータを設定  
        'サイト名'：管理用の名前を設定  
        '物理パス'：運用に使用するフォルダへのパスを設定  
        'バインド'：IPアドレス、'ホスト名（ドメイン名）'を設定  
        'ポート番号'：他のポート番号と異なる番号を設定
        1. 運用に使用するフォルダにWebアクセスユーザ(IIS_IUSRSなど)にアクセス許可を設定

    1. ### IIS URL Rewriteの設定 ==========  
        1. <https://www.iis.net/downloads/microsoft/url-rewrite>から日本語版のURL Rewriteモジュール「rewrite_amd64_ja-JP.msi」をダウンロード
        1. 「rewrite_amd64_ja-JP.msi」を実行（インストール）

    1. ### IISとPHP連携の設定 ==========  
        1. Windows管理ツールから「インターネットインフォメーションサービス(IIS)マネージャ」を起動
        1. 左の欄  
        → 「接続」  
        → PC名を選択（たぶん先頭項目）
        1. 中の欄  
        → IISグループ  
        → 「ハンドラーマッピング」をダブルクリック
        1. ハンドラーマッピングの一覧のパス欄に「.php」がないことを確認
        1. 右の欄  
        → 「操作」  
        → 「モジュールマップの追加」をクリック
        1. 次のパラメータを設定  
        '要求パス'：「*.php」を入力  
        'モジュール'：「FastCgiModule」を選択  
        '実行可能ファイル'：phpフォルダ内の「php-cgi.exe」を選択  
        '名前'：「PHP」を入力  
        1. 「モジュールマップの追加」  
        → 「はい」をクリック
        1. ハンドラーマッピングの一覧のパス欄に「*.php」が追加されたことを確認

    1. ### 動作確認： ==========  
        1. 「C:\inetpub\wwwroot」に'<?php phpinfo(); ?>'が書き込まれた「info.php」を作成
        1. web urlに<http://localhost/info.php>を指定して実行  
        1. 'phpinfo()'の内容が表示されることを確認
        1. 別サイトの場合、運用パスフォルダーに「info.php」を置き、web urlに<http://'IPアドレス:ポート番号'/info.php>を指定して実行  
        1. 'phpinfo()'の内容が表示されることを確認

1. ## mysql
    1. ### mysqlの設定 ==========
        1. <https://dev.mysql.com/downloads/mysql/>から最新バージョン（最新のLTSでないもの）、MicrosoftWindows用、Installer：「mysql-installer-web-community-8.0.46.0」をダウンロード(communityのloginなしで)
        1. 「mysql-installer-web-community-8.0.46.0.msi」を実行
        1. 表示される画面で順次設定  
        'SetupType':ServerOnly、'CheckRequirements':MySQL Serverxxxx（必要な機能（C++モジュール）をインストール）  
        → 本体ダウンロード  
        → インストール  
        → 'ProductConfiguration'  
        　→ 'TypeAndNetworking'：デフォルトのまま  
        　→ 'AuthenticationMethod'：デフォルトのまま  
        　→ 'AccountsAndRoles'：パスワード設定  
        　→ 'WindowsService'：デフォルトのまま  
        　→ 'ServerFilePermissions'：デフォルトのまま  
        → 'ApplyConfiguration'：実行  
        1. 全部チェックされたら終了  
        1. 'ProductConfiguration'のNextクリック  
         → 終了

    1. ### phpmyadminの設定 ==========  
        1. <https://www.phpmyadmin.net>から「phpMyAdmin-5.2.3-all-languages.zip」をダウンロード
        1. 「phpMyAdmin-5.2.3-all-languages.zip」をzip解凍し、解凍してできたフォルダ名を「phpMyAdmin」に変更し、「C:\inetpub\wwwroot」に配置
        1. サーバを再立ち上げ

    1. ### 動作確認： ==========  
        1. web urlに<http://localhost/phpMyAdmin/index.php>を指定して、実行  
        1. phpMyadminのログイン画面が表示  
        1. ログインしてmysqlの操作画面が表示されることを確認

1. ## Irohaboard動作のための環境設定  
    ※ <https://irohaboard.irohasoft.jp/install/>を参照  
    1. ### ソースファイルの配置==========  
        1. <https://irohaboard.irohasoft.jp/download/>から最新版(zipファイル)をダウンロード
        1. zip解凍してできたフォルダ名を適当な名前に変更（例えば「irohaboard」に）し、「C:\inetpub\wwwroot」（別サイトを設定した場合は、運用に使用するフォルダ内）に配置
        1. <https://github.com/MatsuTaka56/irohaboard_tm>から最新版(zipファイル)をダウンロード
        1. zip解凍したフォルダ内の全ファイルを「irohaboard/app」内に置き換え

    1. ### データベースの設定==========  
        1. 「irohaboard/app/Config/database.php」の'host', 'login', 'password', 'database'を必要に応じて変更
        1. phpmyadminを使用して上記'database'で指定した空のデータベースを作成(推奨文字コード : utf8mb4)

    1. ### URL書き換え規則の設定 ========== 
        1. IIS URL書き換え規則ファイル「web.config」を<https://github.com/MatsuTaka56/irohaboard_runtime>からダウンロードし、「irohaboard」フォルダ直下に配置
        1. Windows管理ツールから「インターネットインフォメーションサービス(IIS)マネージャ」を起動
        1. 左の欄  
        → 「接続」  
        → PC名を選択（たぶん先頭項目）  
        → 「サイト」  
        → DefaultWebSite」  
        → irohaboadフォルダ名を選択
        1. 中の欄  
        → IISグループ  
        → 「URL書き換え」をダブルクリック  
        1. 「URL書き換え」に3つの規則が表示されていることを確認

    1. ### irohaboardの設定 ==========  
        1. <http://localhost/install>を実行  
        1. '管理者ログインID', 'パスワード'を設定し、「インストール」をクリック
        1. 「インストールは完了しました」が表示され、インストール終了
        1. 管理システム(<http://localhost/admin>)にログインし、各種設定を実施  
        ・コースの設定 → サンプルコースのインポート  
        ・受講者の追加、受講コースの設定  
        ・その他  
        1. 受講者ログインは、<http://localhost/e-learning>  
        1. 他PCから操作する場合は、IIS動作PCのIPアドレスを確認し、「localhost」をIPアドレスに置き換えたURLを使用
