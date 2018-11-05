FROM php:7.3-fpm-alpine as base

LABEL maintainer="g.iusipov@gmail.com"

RUN set -xe \
    && apk update \
    && apk add --quiet --no-cache \
        icu-dev

COPY --from=gaiaz/php-extensions:7.3-alpine \
    /apcu.so \
    /intl.so \
    /opcache.so \
    /pdo_mysql.so \
    /usr/local/lib/php/extensions/no-debug-non-zts-20180731/

RUN docker-php-ext-enable \
        apcu \
        intl \
        opcache \
        pdo_mysql

COPY ./conf.d/core.ini \
    ./conf.d/realpath.ini \
    /usr/local/etc/php/conf.d/

WORKDIR /app

# prod target
FROM base as prod

COPY ./conf.d/opcache.prod.ini \
    /usr/local/etc/php/conf.d/

COPY . /app

USER docker

# test target
FROM base as test

COPY ./conf.d/opcache.prod.ini \
    /usr/local/etc/php/conf.d/

# Installing PHP CS Fixer
RUN curl -sSo /usr/local/bin/php-cs-fixer https://get.sensiolabs.org/php-cs-fixer.phar \
    && chmod +x /usr/local/bin/php-cs-fixer

COPY . /app

USER docker

# dev target
FROM base as dev

# Installing Xdebug
COPY --from=gaiaz/php-extensions \
    /xdebug.so \
    /usr/local/lib/php/extensions/no-debug-non-zts-20180731/

RUN docker-php-ext-enable \
        xdebug

COPY ./conf.d/apcu.dev.ini \
    ./conf.d/xdebug.dev.ini \
    /usr/local/etc/php/conf.d/

# Installing Composer
RUN curl --silent --show-error \
        https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Installing CacheTool
RUN curl --silent --show-error --output /usr/local/bin/cachetool \
        https://gordalina.github.io/cachetool/downloads/cachetool.phar \
    && chmod +x /usr/local/bin/cachetool

# Installing PHP CS Fixer
RUN curl --silent --show-error --output /usr/local/bin/php-cs-fixer \
        https://get.sensiolabs.org/php-cs-fixer.phar \
    && chmod +x /usr/local/bin/php-cs-fixer

# fixuid
RUN USER=docker \
    && GROUP=docker \
    && addgroup -g 1000 $GROUP \
    && adduser -u 1000 -G $GROUP -h /home/docker -s /bin/sh -D $USER \
    && curl -SsL https://github.com/boxboat/fixuid/releases/download/v0.4/fixuid-0.4-linux-amd64.tar.gz | tar -C /usr/local/bin -xzf - \
    && chown root:root /usr/local/bin/fixuid \
    && chmod 4755 /usr/local/bin/fixuid \
    && mkdir -p /etc/fixuid \
    && printf "user: $USER\ngroup: $GROUP\n" > /etc/fixuid/config.yml

ENTRYPOINT ["sh", "-c", "fixuid;docker-php-entrypoint php-fpm"]
