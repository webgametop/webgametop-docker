FROM php:8.5-cli

RUN set -ex \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        vim nano \
        curl unzip \
        git iproute2 procps \
        libpq-dev \
        libicu-dev \
        libzip-dev \
        default-mysql-client \
        postgresql-client \
        redis-tools \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN pecl install redis \
    && docker-php-ext-enable redis

RUN docker-php-ext-configure pgsql \
        --with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install -j$(nproc) \
        pdo \
        pdo_mysql \
        mysqli \
        pdo_pgsql \
        pgsql \
        intl \
        zip \
        bcmath

RUN pecl clear-cache || true

COPY --from=composer:2.8 /usr/bin/composer /usr/bin/composer

COPY ./php/php.ini /usr/local/etc/php/conf.d/custom.ini

RUN mkdir -p /var/log/php \
    && touch /var/log/php/errors.log \
    && chmod 666 /var/log/php/errors.log

ENV COMPOSER_ALLOW_SUPERUSER=1
ENV PATH="${PATH}:/var/www/vendor/bin"

WORKDIR /var/www/www-data

CMD ["php", "-a"]
