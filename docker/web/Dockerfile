FROM php:8.2.11-apache

# 各種パッケージをインストール
RUN apt-get update \
  && apt-get install -y vim git gh zlib1g-dev mariadb-client curl gnupg libzip-dev \
  && docker-php-ext-install zip pdo_mysql

# Composer をインストール
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
RUN php -r "if (hash_file('SHA384', 'composer-setup.php') === trim(file_get_contents('https://composer.github.io/installer.sig'))) { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
RUN php composer-setup.php
RUN php -r "unlink('composer-setup.php');"
RUN mv composer.phar /usr/local/bin/composer

# Composer が root ユーザーで実行できるように設定
ENV COMPOSER_ALLOW_SUPERUSER 1

# Composer のパスを通す
ENV COMPOSER_HOME /composer
ENV PATH $PATH:/composer/vendor/bin

# Node.js をインストール
RUN curl -sL https://deb.nodesource.com/setup_current.x | bash -
RUN apt-get install -y nodejs
RUN npm install -g npm@latest
RUN npm install -g yarn@latest
RUN npm install -g pnpm@latest

# MailHog でメールを受信できるように設定
RUN curl -sSL https://github.com/mailhog/mhsendmail/releases/download/v0.2.0/mhsendmail_linux_amd64 -o mhsendmail \
    && chmod +x mhsendmail \
    && mv mhsendmail /usr/local/bin/mhsendmail

# php.ini をコンテナにコピー
COPY php.ini /usr/local/etc/php/

# GitHub 設定
RUN git config --global user.email "temporary_user@example.com" && git config --global user.name "Temporary User"

# 日本語入力できるように設定
ENV LANG C.UTF-8
ENV LANGUAGE en_US:

# 作業ディレクトリ設定
WORKDIR /var/www

# www ディレクトリの中身をクリアするコマンドをコンテナにコピー
COPY clear-www /usr/local/bin/
RUN chmod 755 /usr/local/bin/clear-www
