FROM php:7.2-fpm-alpine as base

LABEL maintainer="g.iusipov@gmail.com"

ENV BUILD_DEPS \
    autoconf \
    g++ \
    make

RUN set -xe && \
    apk add --no-cache \
        tzdata \
        icu-dev && \
    docker-php-ext-install \
        intl \
        pdo_mysql \
        opcache && \
    # Installing PECL Extensions
    apk add --no-cache --virtual .build-deps \
        $BUILD_DEPS && \
    for key in \
        apcu \
        xdebug \
    ; do \
        pecl install $key && \
        docker-php-ext-enable $key \
    ; done && \
    apk del .build-deps

FROM base as dev

COPY dev.ini /usr/local/etc/php/conf.d/

# Installing Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Installing CacheTool
RUN curl -sSo /usr/local/bin/cachetool https://gordalina.github.io/cachetool/downloads/cachetool.phar && \
    chmod +x /usr/local/bin/cachetool

# Installing PHP CS Fixer
RUN curl -sSo /usr/local/bin/php-cs-fixer http://get.sensiolabs.org/php-cs-fixer.phar && \
    chmod +x /usr/local/bin/php-cs-fixer

# fixuid
RUN USER=docker && \
    GROUP=docker && \
    addgroup -g 1000 $GROUP && \
    adduser -u 1000 -G $GROUP -h /home/docker -s /bin/sh -D $USER && \
    curl -SsL https://github.com/boxboat/fixuid/releases/download/v0.4/fixuid-0.4-linux-amd64.tar.gz | tar -C /usr/local/bin -xzf - && \
    chown root:root /usr/local/bin/fixuid && \
    chmod 4755 /usr/local/bin/fixuid && \
    mkdir -p /etc/fixuid && \
    printf "user: $USER\ngroup: $GROUP\n" > /etc/fixuid/config.yml

ENTRYPOINT ["sh", "-c", "fixuid;docker-php-entrypoint php-fpm"]
