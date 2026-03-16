FROM php:8.5-fpm

RUN set -ex \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        vim nano \
        curl unzip \
        git iproute2 \
        libpq-dev \
        libicu-dev \
        libzip-dev \
        libpng-dev \
        libjpeg-dev \
        libwebp-dev \
        libfreetype6-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN pecl install redis xdebug \
    && docker-php-ext-enable redis xdebug

RUN docker-php-ext-configure pgsql \
        --with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install -j$(nproc) \
        gd \
        pdo \
        pdo_mysql \
        mysqli \
        pdo_pgsql \
        pgsql \
        intl \
        zip \
        bcmath \
    && docker-php-ext-configure gd \
           --with-freetype \
           --with-jpeg \
           --with-webp

RUN pecl clear-cache || true

COPY --from=composer:2.8 /usr/bin/composer /usr/bin/composer

COPY ./php/xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
COPY ./php/php.ini /usr/local/etc/php/conf.d/custom.ini

RUN mkdir -p /var/log/php \
    && touch /var/log/php/errors.log /var/log/php/xdebug.log \
    && chmod 666 /var/log/php/*.log

WORKDIR /var/www/www-data

HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
    CMD php -r "if (fsockopen('127.0.0.1', 9000)) exit(0); exit(1);"

CMD ["php-fpm"]
