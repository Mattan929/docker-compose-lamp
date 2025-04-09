FROM php:7.4.2-apache-buster

ARG DEBIAN_FRONTEND=noninteractive

# Обновление пакетов и установка зависимостей
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
    apt-utils \
    nano \
    wget \
    dialog \
    libsqlite3-dev \
    libsqlite3-0 \
    default-mysql-client \
    zlib1g-dev \
    libzip-dev \
    libicu-dev \
    build-essential \
    git \
    curl \
    libonig-dev \
    iputils-ping \
    libcurl4 \
    libcurl4-openssl-dev \
    zip \
    openssl \
    libmagickwand-dev \
    libc-client-dev \
    libkrb5-dev \
    libpq-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    gnupg \
    gdebi-core \
    libpcsclite1 \
    pcscd \
    libssl-dev \
    libsodium-dev \
    libzmq3-dev \
    libtool \
    autoconf \
    uuid-dev \
    pkg-config && \
    rm -rf /var/lib/apt/lists/*

# Устанавливаем Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Устанавливаем xdebug
RUN pecl install xdebug-3.1.4 && \
    docker-php-ext-enable xdebug && \
    mkdir /var/log/xdebug

# Устанавливаем redis
RUN pecl install redis-5.1.1 && \
    docker-php-ext-enable redis

# Устанавливаем imagick
RUN apt-get update && \
    apt-get install -y --no-install-recommends libmagickwand-dev && \
    rm -rf /var/lib/apt/lists/* && \
    pecl install imagick && \
    docker-php-ext-enable imagick

# Устанавливаем imap
RUN apt-get update && \
    apt-get install -y --no-install-recommends libc-client-dev libkrb5-dev && \
    rm -rf /var/lib/apt/lists/* && \
    PHP_OPENSSL=yes docker-php-ext-configure imap --with-kerberos --with-imap-ssl && \
    docker-php-ext-install imap

# Другие PHP-расширения
RUN docker-php-ext-install pdo_mysql pdo_sqlite bcmath pcntl mysqli curl tokenizer json zip intl mbstring gettext calendar exif

# Устанавливаем PostgreSQL PDO
RUN apt-get install -y libpq-dev && \
    docker-php-ext-install pdo pdo_pgsql pgsql

# Устанавливаем Freetype и gd
RUN apt-get install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure gd --enable-gd --with-freetype --with-jpeg && \
    docker-php-ext-install gd

# Включаем SSL и настройку Apache
RUN mkdir -p /etc/apache2/ssl && \
    a2enmod ssl rewrite headers

# Устанавливаем CSP
COPY ./linux-arm64_deb/ /tmp/csp-debs/
RUN dpkg -i /tmp/csp-debs/*.deb || apt-get install -fy
ENV PATH="/opt/cprocsp/bin/aarch64:${PATH}"

# Проверка установки CSP
RUN which cryptcp && echo "cryptcp found" || echo "cryptcp not found"
RUN which csrsign && echo "csrsign found" || echo "csrsign not found"

# Очистка временных файлов
RUN rm -rf /usr/src/* /tmp/* /var/tmp/*
