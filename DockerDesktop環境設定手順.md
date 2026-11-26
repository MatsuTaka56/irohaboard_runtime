# Docker Desktop環境の構築手順
Docker Desktop環境でirohaboardを動作させるためのもろもろの設定手順をまとめます。
※私が動作させたときの一例です。

1. ## Docker Desktop環境の構築
    1. ### Windowsサブシステム設定
        1. 「コントロールパネル」を起動
        1. 「プログラムと機能」 → 「Windowsの既往の有効化または無効化」を選択
        1. 「Linux用Windowsサブシステム」, 「コンテナー」, 「メディア機能」, 「ワークフォルダークライアント」, 「仮想マシンプラットフォーム」をチェック  
        1. 「OK」をクリックし、更新

    1. ### WSL設定
        1. 管理者用WindowsPowerShellを起動
        1. 'wsl --update'を実行
        1. 'wsl --install -d Ubuntu-20.04'を実行  
        → インストール終了後、Ubuntu-20.04が起動
        1. 「Create a default Unix user account:」に対し、ユーザアカウントを入力
        1. 「New password:」に対し、パスワードを入力
        1. 「Retype new password:」に対し、パスワードを再入力
        1. コマンド入力待ちに対し、'exit'を入力し、Ubuntuを終了  
        ※BIOSで仮想マシン設定が「off」の場合は、「on」に変更後、再実行

    1.  ### Docker Desktop Installer入手
        1. <https://www.docker.com> 画面で「Get Started」を選択
        1. 上部メニューバーの「Products」にカーソル移動
        1. プルダウンメニューのProducts一覧で「Docker Desktop」を選択
        1. 「Docker Desktop」画面で「Download Docker Desktop」にカーソル移動
        1. プルダウンメニューの「Download for Windows - AMD64」を選択
        1. 「Docker Desktop Installer」がダウンロード

    1. ### Docker Desktopインストール
        1. 「Docker Desktop Installer」を実行
        1. 「Configuration」画面で「Per-user installation」, 「WSL2」を選択
        1. 「Add shortcut to desktop」をチェックし、「OK」をクリック  
        → 「Unpacking files... 」インストール実行経過画面表示
        1. 「Installation succeeded」画面で「Close」をクリック

    1. ### Docker Desktop起動
        1. デスクトップ画面上の「Docker Desktop」を起動
        1. 「Docker Subscription Service Agreement」画面で「Accept」クリック
        1. 「Welcome toDocker」画面で「skip」クリック
        1. 「docker desktop」のトップ画面表示  
        ※次回からは直接「docker desktop」のトップ画面が表示される

    1. ### Docker Desktop確認
        1. Windows PowerShellを起動
        1. 'docker version'を実行
        1. Client, ServerのVersionなどを確認

1. ## irohaboard環境の構築
    1. ### irohaboard動作環境（フォルダ、ファイル構成）構築
        1. 「irohaboard」フォルダ(irohaboard構築のベースフォルダ)を作成  
        1. <https://github.com/MatsuTaka56/irohaboard_runtime>から「Docker構築用ファイル類」をダウンロード  
        1. 「Docker構築用ファイル類」から「docker-compose.yml」, 「Dockerfile」（Docker構築用ファイル）を「irohaboard」フォルダにコピー
        1. 「irohaboard」フォルダ内に「src」, 「src/e-learning」フォルダを作成
        1. 「Docker構築用ファイル類」から「index.php」, 「info.php」（動作確認用のテストphp）を「src」フォルダにコピー
        1. <https://irohaboard.irohasoft.jp/download/>から最新版(zipファイル)をダウンロード
        1. zip解凍してできたフォルダ内の全ファイルを「src/e-learning」フォルダにコピー
        1. <https://github.com/MatsuTaka56/irohaboard_tm>から最新版(zipファイル)をダウンロード
        1. zip解凍したフォルダ内の全ファイルを「src/e-learning/app」内に置き換え
        1. <https://github.com/MatsuTaka56/irohaboard_runtime>からサンプルコースをダウンロードし、「src」フォルダにコピー  
        「src/サンプルコース/サンプルコースcsv, サンプルコース_files.zip」
        1. 動作環境（フォルダ、ファイル構成）は以下の通り  
        　irohaboard/  
        　　src/  
        　　　e-learning/  
        　　　　app/  
        　　　　lib/  
        　　　サンプルコース/  
        　　　　サンプルコースcsv, サンプルコース_files.zip  
        　　docker-compose.yml  
        　　Dockerfile  

    1. ### irohaboard用コンテナの作成
        1. Windows PowerShellを起動
        1. irohaboardのベースフォルダに位置付ける(docker-compose.ymlがあるフォルダ)  
        1. 'docker compose up -d --build'を実行
        1. 「dockerDesktop」のContainersに「irohaboard/mysql-db」, 「php-apache-app」, 「phpmyadmin-tool」が登録されていることを確認

    1. ### irohaboardの設定
        1. <http://localhost:8081>を実行  
        → phpMyAdminが起動
        1. データベースの新規作成で「irohaboard/utf8mb4_general_ci」を作成 
        1. irohaboard/src/e-learning/app/Config/database.phpを以下のように更新  
        'host' => 'mysql',                // MySQLサーバのホスト名  
        'login' => 'root',                // MySQLの設定に合わす  
        'password' => 'rootpass',         // MySQLの設定に合わす  
        'database' => 'irohaboard',       // 作成したデータベースに合わす  
        'encoding' => 'utf8mb4'           // 作成したデータベースに合わす  
        1. ブラウザーの別タブで<http://localhost:8080/e-learning/install>を実行  
        1. 「管理者ログインID」, 「パスワード」を設定し、「インストール」をクリック
        1. 「インストールは完了しました」が表示され、インストール終了
        1. 管理システム(<http://localhost:8080/e-learning/admin>)にログインし、各種設定を実施  
        ・コースの設定=>サンプルコースのインポート  
        ・受講者の追加、受講コースの設定  
        ・その他  
        1. 受講者ログインは、<http://localhost:8080/e-learning>  
        1. 他PCから操作する場合は、Docker Desktop動作PCのIPアドレスを確認し、「localhost」をIPアドレスに置き換えたURLを使用
