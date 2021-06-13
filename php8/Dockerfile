FROM 8565012/php:8.0

# 这里尽量使用原始配置文件，然后在conf.d目录新建php.ini覆盖设置
# 可以明确变更项
RUN cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini

RUN apt-get update && \
    docker-php-ext-install mysqli

# Xdebug扩展安装
# https://xdebug.org/docs/compat
ARG INSTALL_XDEBUG=false
RUN if [ ${INSTALL_XDEBUG} = true ]; then \
    pecl install xdebug && \
    docker-php-ext-enable xdebug \
;fi