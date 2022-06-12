ARG VARIANT=7.4-bullseye
FROM mcr.microsoft.com/vscode/devcontainers/php:${VARIANT}

ARG NODE_VERSION="16"
RUN if [ "${NODE_VERSION}" != "none" ]; then su vscode -c "umask 0002 && . /usr/local/share/nvm/nvm.sh && nvm install ${NODE_VERSION} 2>&1"; fi

# [Optional] Uncomment this section to install additional OS packages.
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends libpng-dev libicu-dev libzip-dev

RUN docker-php-ext-install pdo pdo_mysql \
    && docker-php-ext-configure gd \
    && docker-php-ext-install gd \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl \
    && docker-php-ext-configure zip \
    && docker-php-ext-install zip

COPY configurations/*.conf /etc/apache2/sites-available/

RUN mv "$PHP_INI_DIR/php.ini-production" "$PHP_INI_DIR/php.ini" \
    && mkdir /var/www/html/public \
    && a2enmod rewrite \
    && a2dissite 000-default.conf \
    && a2ensite winter-8080.conf
echo $'first line \n\
second line \n\
third line' > /etc/nginx/nginx.conf
RUN echo "ServerName 127.0.0.1 \n" \
    "Mutex posixsem\n" >> /etc/apache2/apache2.conf \
    && service apache2 restart

CMD ["/usr/sbin/apache2ctl", "-DFOREGROUND"]
