# default environment variable = development
ARG ENVIRONMENT=development

# **********************************************************************************
# 1. Base Image
FROM php:8.2-fpm AS base

# Install system dependencies + PHP extensions
RUN apt-get update && apt-get install -y \
    git unzip libzip-dev \
    && docker-php-ext-install pdo_mysql zip

# Install Composer
COPY --from=composer:2 /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

# **********************************************************************************
# 2. Development Stage
FROM base AS development

# Install additional PHP extensions for development
RUN pecl install xdebug && docker-php-ext-enable xdebug

RUN echo "xdebug.mode=debug" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.client_port=9003" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

CMD ["php-fpm"]

# **********************************************************************************
# 3. Build Stage
FROM base AS build

COPY ./laravel /var/www/html/

RUN composer install --no-interaction --optimize-autoloader

RUN chown -R www-data:www-data /var/www/html \
    && chmod -R 775 storage bootstrap/cache
    
# **********************************************************************************
# 4. Production Stage
FROM base AS production

COPY --from=build /var/www/html /var/www/html

USER www-data

CMD ["php-fpm"]

# **********************************************************************************
# 5. Final Stage
FROM ${ENVIRONMENT} AS final