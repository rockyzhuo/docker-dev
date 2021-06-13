# https://docs.docker.com/engine/reference/builder/#understand-how-arg-and-from-interact
FROM php:8.0-fpm

LABEL description="PHP8.0-fpm 基础镜像，\
包括 git、composer、redis、swole、unzip，\
开启的插件opcache，gd、pdo_mysql、bcmath、zip。"

# 却换到阿里云软件源
# https://developer.aliyun.com/mirror/debian
RUN sed -i 's/deb.debian.org/mirrors.cloud.tencent.com/g' /etc/apt/sources.list && \
    sed -i 's/security.debian.org/mirrors.cloud.tencent.com/g' /etc/apt/sources.list

RUN apt-get update

# 安装git
RUN apt-get install -y \
    git

# composer安装，使用阿里云镜像
RUN curl -o /usr/local/bin/composer https://mirrors.aliyun.com/composer/composer.phar && \
    chmod +x /usr/local/bin/composer && \
    composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/

# 调整时区
RUN ln -sf /usr/share/zoneinfo/PRC /etc/localtime && \
    echo PRC > /etc/timezone

# 安装swoole与redis
# 启用ssl
# apt-get install libssl-dev -y && \
# printf "\nyes\n" | pecl install swoole && \
RUN pecl install swoole && \
    docker-php-ext-enable swoole && \
    pecl install redis && \
    docker-php-ext-enable redis

# gd扩展单独处理
RUN apt-get install -y \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libwebp-dev && \
    docker-php-ext-configure gd --with-freetype --with-jpeg --with-png --with-webp && \
    # -j 多进程安装 $(nproc)最大进程数
    docker-php-ext-install -j$(nproc) gd

# php扩展安装
RUN apt-get install -y libzip-dev unzip&& \
    docker-php-ext-install -j$(nproc) \
    pdo_mysql \
    opcache \
    bcmath \
    zip

# 清理
RUN rm -rf /var/lib/apt/lists/*