ARG DOCKER_PHP_VERSION

FROM php:7.4-fpm-alpine

ARG DOCKER_PHP_ENABLE_XDEBUG='on'
ARG TZ='UTC'

# https://wiki.alpinelinux.org/wiki/Setting_the_timezone
RUN echo "${TZ}" && apk --update add tzdata && \
    cp /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    apk del tzdata

RUN apk add --update --no-cache icu-libs \
        libintl \
        build-base \
        zlib-dev \
        cyrus-sasl-dev \
        libgsasl-dev \
        oniguruma-dev \
        procps \
        imagemagick \
        patch \
        bash \
        ldb-dev \
        libldap \
        openldap-dev \
        htop \
        acl \
        apk-cron \
        augeas-dev \
        autoconf \
        curl \
        ca-certificates \
        dialog \
        freetype-dev \
        gomplate \
        git \
        gcc \
        gettext-dev \
        icu-dev \
        libcurl \
        libffi-dev \
        libgcrypt-dev \
        libjpeg-turbo-dev \
        libpng-dev \
        libmcrypt-dev \
        libressl-dev \
        libxslt-dev \
        libzip-dev \
        linux-headers \
        libxml2-dev \
        ldb-dev \
        make \
        musl-dev \
        mysql-client \
        openssh-client \
        pcre-dev \
        ssmtp \
        sqlite-dev \
        supervisor \
        su-exec \
        wget 
#  Install php extensions
RUN php -m && \
    docker-php-ext-install exif \
        mysqli \
        opcache \
        xsl \
        bcmath \
        gd \
        gettext \
        intl \
        opcache \
        pcntl \
        soap \
        zip \
        sockets \
        calendar \
        pdo_mysql && \
    docker-php-ext-configure gd --with-jpeg --with-freetype && \
	docker-php-ext-install gd && \
    pecl install redis && \
    docker-php-ext-enable redis && \
    apk add --update --no-cache --virtual .docker-php-mongodb-dependencies \
    heimdal-dev && \
    pecl install mongodb && \
    pecl install xdebug-2.8.1 && \
    docker-php-ext-enable mongodb xdebug &&\
    apk del .docker-php-mongodb-dependencies && \
    docker-php-ext-configure ldap &&\
    docker-php-ext-install ldap && \
    apk add --update --no-cache \
        libpq && \
    # Build dependancies for PostgreSQL \
    apk add --update --no-cache --virtual .docker-php-postgresql-dependencies \
        postgresql-client \
        postgresql-dev && \
    docker-php-ext-configure pdo_pgsql && \
    docker-php-ext-configure pgsql && \
    docker-php-ext-install pdo_pgsql \
        pgsql && \
    apk del .docker-php-postgresql-dependencies

# Enable Xdebug
RUN if [ "${DOCKER_PHP_ENABLE_XDEBUG}" == "on" ]; then \
      yes | pecl install xdebug && \
      echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini && \
      echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini && \
      echo "xdebug.remote_autostart=off" >> /usr/local/etc/php/conf.d/xdebug.ini && \
      php -m; \
    else \
      echo "Skip xdebug support"; \
    fi

# Clean
RUN rm -rf /var/cache/apk/* && docker-php-source delete

USER root

RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

USER www-data:www-data

WORKDIR /var/www/
