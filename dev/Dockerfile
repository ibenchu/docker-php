FROM php:fpm-alpine
MAINTAINER HuadongZuo <admin@zuohuadong.cn>
# RUN apk update && \
#     apk upgrade
RUN echo "http://nl.alpinelinux.org/alpine/latest-stable/main" > /etc/apk/repositories \
&& echo "http://nl.alpinelinux.org/alpine/edge/testing/" >> /etc/apk/repositories \
&& echo "http://nl.alpinelinux.org/alpine/edge/community/" >> /etc/apk/repositories \
&& echo "nameserver 119.29.29.29" >> /etc/resolv.conf && apk update && apk upgrade
# Install modules : GD mcrypt iconv
RUN apk add --no-cache freetype-dev libwebp-dev libjpeg-turbo libpng-dev libjpeg-turbo-dev && \
  docker-php-ext-configure gd \
    --with-gd \
    # --with-zlib \
    --with-freetype-dir=/usr/include/ \
    --with-png-dir=/usr/include/ \
    --with-webp-dir=/usr/include/ \
    --with-jpeg-dir=/usr/include/ && \
  NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) && \
  docker-php-ext-install -j${NPROC} gd && \
  apk del --no-cache freetype-dev libpng-dev libjpeg-turbo-dev libwebp-dev
RUN set -x && \
    apk --no-cache add -t .build-deps \
    libmcrypt-dev \
    build-base \
    linux-headers \
    libaio \
    gcc \
    g++ \
    zlib-dev \
    autoconf \
    postgresql-dev \
    libpq
    # freetype \
    
# RUN apk --no-cache add libmcrypt-dev \
#         build-base
#         freetype \
#         freetype-dev \
#         libjpeg-turbo-dev \
#         libpng-dev \
#         libpq \     
# RUN docker-php-ext-install 
RUN docker-php-ext-install mcrypt zip iconv



# install php pdo_mysql redis
RUN docker-php-ext-install pdo_mysql mysqli mbstring json opcache fileinfo
RUN echo "opcache.enable_cli=1" >>  /usr/local/etc/php/conf.d/docker-php-ext-opcache.ini
    # && echo "extension=redis.so" > /usr/local/etc/php/conf.d/redis.ini

# install php pdo_pgsql
RUN docker-php-ext-install pdo_pgsql pgsql


# install swoole
RUN pecl install redis
# RUN docker-php-ext-enable swoole
RUN docker-php-ext-enable redis

RUN pecl install swoole

RUN docker-php-ext-enable swoole
# log to /var/www/log
# RUN mkdir -p /var/www/log
# RUN echo "error_log = /var/www/log/php_error.log" > /usr/local/etc/php/conf.d/log.ini
RUN  mkdir /home/wwwroot && mkdir /home/log && mkdir /home/log/php
RUN echo "log_errors = On" >> /usr/local/etc/php/conf.d/log.ini \
    && echo "error_log=/home/log/php" >> /usr/local/etc/php/conf.d/log.ini
RUN chown -R www-data:www-data  /home/wwwroot

    # Clean-up.
RUN cd && \
    apk del .build-deps && \
    rm -rf /tmp/*
    # Forward request and error logs to docker log collector.


COPY php.ini /usr/local/etc/php/
# COPY php-fpm.conf /usr/local/etc/php/

EXPOSE 9000 9501 9502 9503 9504 9505 9506 9507 9508 9509 9510
# add user additional conf for apache & php
# RUN echo "" >> /usr/local/php/conf.d/additional.ini
# RUN echo "" >> /etc/apache2/conf-enabled/additional.conf
#RUN sed -i -e 's/listen = 127.0.0.1:9000/listen = 9000/' /usr/local/php/etc/php-fpm.d/www.conf
# set system timezone & php timezone
# @TODO
# ENTRYPOINT ["/usr/local/php/sbin/php-fpm", "-F", "-c", "/usr/local/php/lib/php.ini"]
CMD ["php-fpm"]