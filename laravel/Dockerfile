# this dockerfile is used for creating an image with assets
FROM node:14.17 as node_builder

USER root

COPY --chown=www-data:www-data . /var/www

WORKDIR /var/www

RUN yarn && yarn mix

FROM php:8.1-fpm as app

USER root

COPY --chown=www-data:www-data --from=node_builder /var/www /var/www

COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

WORKDIR /var/www

RUN apt-get update -y && apt-get install git zip -y

RUN docker-php-ext-install pdo_mysql && docker-php-ext-install pdo && docker-php-ext-enable opcache

RUN apt-get install -y mariadb-client

RUN composer install --optimize-autoloader --no-dev

RUN php artisan config:cache && php artisan route:cache && php artisan view:cache
