# 公式のPHP8.5.12+Apacheイメージを使用
FROM php:8.5.12-apache

# 1. MySQL / MariaDB接続に必要な拡張モジュールをインストール
RUN apt-get update \
 && apt-get install -y libzip-dev zip unzip \
 && docker-php-ext-install pdo_mysql zip mysqli

# 2. SSL(HTTPS)用のApache設定
RUN a2enmod ssl && a2ensite default-ssl

# 3. ApacheのRewriteモジュールを有効化する
RUN a2enmod rewrite headers ssl

# 4. AllowOverride を有効化
RUN sed -i 's/AllowOverride None/AllowOverride All/' \
    /etc/apache2/apache2.conf

# 5. ローカル開発用のSSL自己証明書を自動生成
RUN apt-get update &&apt-get install -y openssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/ssl/private/ssl-cert-snakeoil.key \
    -out /etc/ssl/certs/ssl-cert-snakeoil.pem \
    -subj "/C=JP/ST=Tokyo/L=Tama/O=Development/OU=Web/CN=localhost"

# 6. Apacheのドキュメントルートを考慮し、作業ディレクトリを設定
WORKDIR /var/www/html


