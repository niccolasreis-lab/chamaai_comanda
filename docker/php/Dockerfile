FROM php:8.3-fpm-alpine

RUN apk add --no-cache icu-dev libzip-dev $PHPIZE_DEPS \
    && docker-php-ext-install intl pdo_mysql zip bcmath opcache \
    && pecl install redis \
    && docker-php-ext-enable redis \
    && apk del $PHPIZE_DEPS

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
COPY docker/php/php.ini /usr/local/etc/php/conf.d/chamaai.ini

WORKDIR /var/www/html
