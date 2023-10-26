FROM php:8.2-rc-fpm-bullseye AS base
ARG UID=1000
ARG GID=1000
ARG CS_FIXER_VERSION=3.9.2
ARG SECURITY_CHECKER_VERSION=1.2.0

# Install necessary dependencies for development
RUN apt-get update && apt-get install -y --no-install-recommends \
    libzip-dev \
    libev-dev \
    librabbitmq-dev \
    libssl-dev \
    libxml2-dev \
    supervisor \
    unzip \
    && pecl install amqp-1.11.0 pcov redis rdkafka-dev sockets apcu \
    && docker-php-ext-install -j$(nproc) bcmath intl mysqli pdo_mysql zip sockets

RUN groupadd -g $GID dmanov \
    && useradd -d /home/dmanov -s /bin/bash -u $UID -g $GID dmanov \
    && mkdir -p /home/dmanov/app /var/supervisor \
    && chown -R dmanov:dmanov /home/dmanov /var/supervisor /var/log/supervisor /etc/supervisor/conf.d

# Base PHP configuration
COPY docker/php/config/php-fpm.conf /usr/local/etc/php-fpm.d/

# Copy Supervisor configuration
COPY config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf


# Composer setup
WORKDIR /home/dmanov/app
ENV COMPOSER_ALLOW_SUPERUSER=1
COPY --from=composer:2.6 /usr/bin/composer /usr/bin/composer
COPY ./composer.* /home/dmanov/app


FROM base AS dev

# Dev PHP configuration
COPY docker/php/config/php_dev.ini /usr/local/etc/php/conf.d/php.ini

# Composer install
RUN composer update
RUN composer install --prefer-dist --no-dev --no-scripts --no-progress --no-interaction

# Install Xdebug
RUN pecl install xdebug \
&& docker-php-ext-enable xdebug

# Composer run
USER root
RUN composer dump-autoload --optimize

USER dmanov

# ================================================
FROM base AS Test

# Test PHP configuration
COPY docker/php/config/php_test.ini /usr/local/etc/php/conf.d/php.ini

# Composer install
RUN composer update
RUN composer install --prefer-dist --no-dev --no-scripts --no-progress --no-interaction

COPY . /home/dmanov/app
#COPY ./.env.test /home/dmanov/app/.env

# Composer run
USER root
RUN composer dump-autoload --optimize

USER dmanov

## ================================================
FROM base AS Prod

# Test PHP configuration
COPY docker/php/config/php_prod.ini /usr/local/etc/php/conf.d/php.ini

# Composer install
RUN composer update
RUN composer install --prefer-dist --no-dev --no-scripts --no-progress --no-interaction

COPY . /home/dmanov/app
#COPY ./.env.prod /home/dmanov/app/.env

# Composer run
USER root
RUN composer dump-autoload --optimize

USER dmanov