FROM 8565012/php:5.6

# 这里尽量使用原始配置文件，然后在conf.d目录新建php.ini覆盖设置
# 可以明确变更项
RUN cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini

RUN apt-get update && \
    pecl channel-update pecl.php.net

# 安装swoole与redis
# 启用ssl
# apt-get install libssl-dev -y && \
# printf "\nyes\n" | pecl install swoole && \
RUN pecl install http://pecl.php.net/get/redis-3.1.6.tgz && \
    docker-php-ext-enable redis && \
    apt-get install -y librabbitmq-dev && \
    pecl install amqp && \
    docker-php-ext-enable amqp

RUN apt-get install -y libsodium-dev && \
    pecl install https://pecl.php.net/get/libsodium-1.0.7.tgz && \
    docker-php-ext-enable libsodium

# Xdebug扩展安装
# https://xdebug.org/docs/compat
ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    pecl install http://pecl.php.net/get/xdebug-2.5.5.tgz && \
    docker-php-ext-enable xdebug \
;fi

# 清理
RUN rm -rf /var/lib/apt/lists/*