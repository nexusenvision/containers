FROM alpine:latest

WORKDIR /var/www/html/

# Essentials
RUN echo "UTC" > /etc/timezone \
    && apk add --no-cache zip unzip curl sqlite python3-dev python3 nginx supervisor git nodejs npm nano

# Installing bash
RUN apk add bash \
    && sed -i 's/bin\/ash/bin\/bash/g' /etc/passwd

# Installing PHP
RUN apk add --no-cache php8 \
    php8-common \
    php8-fpm \
    php8-pdo \
    php8-opcache \
    php8-zip \
    php8-phar \
    php8-iconv \
    php8-cli \
    php8-curl \
    php8-openssl \
    php8-mbstring \
    php8-tokenizer \
    php8-fileinfo \
    php8-json \
    php8-xml \
    php8-xmlwriter \
    php8-simplexml \
    php8-dom \
    php8-pdo_mysql \
    php8-pdo_sqlite \
    php8-tokenizer \
    php8-pecl-redis \
    php8-intl \
    php8-gd

# Installing composer
RUN curl -sS https://getcomposer.org/installer -o composer-setup.php \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && rm -rf composer-setup.php

# test install laravel9
# RUN composer create-project laravel/laravel ../laravel

# Configure supervisor
RUN mkdir -p /etc/supervisor.d/
COPY .docker/etc/supervisor.d/supervisord.ini /etc/supervisor.d/supervisord.ini

# Configure PHP
RUN mkdir -p /run/php/ \
    && touch /run/php/php8.0-fpm.pid

COPY .docker/etc/php8/php-fpm.conf /etc/php8/php-fpm.conf

RUN sed -i 's/memory_limit = 128M/memory_limit = 256M/g' /etc/php8/php.ini \
    && sed -i 's/post_max_size = 8M/post_max_size = 16M/g' /etc/php8/php.ini \
    && sed -i 's/upload_max_filesize = 2M/upload_max_filesize = 16M/g' /etc/php8/php.ini

# Configure nginx
COPY .docker/etc/nginx/nginx.conf /etc/nginx/
COPY .docker/etc/nginx/modules/nginx-laravel.conf /etc/nginx/modules/

RUN mkdir -p /run/nginx/ \
    && touch /run/nginx/nginx.pid

RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

RUN mkdir public && touch public/index.php && echo "Hallow World" >> public/index.php
# RUN mkdir storage && chown -R nobody:nobody /var/www/laravel/storage

EXPOSE 80
CMD ["supervisord", "-c", "/etc/supervisor.d/supervisord.ini"]
