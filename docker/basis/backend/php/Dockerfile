FROM php:8.1-fpm-alpine

RUN apk add --update --no-cache libintl icu icu-dev pcre-dev $PHPIZE_DEPS \
 && pecl install xdebug-3.1.4 \
 && docker-php-ext-enable xdebug \
 && docker-php-ext-install intl pdo mysqli \
 && cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini \
 && sed -i 's/variables_order = "GPCS"/variables_order = "EGPCS"/g' /usr/local/etc/php/php.ini-*


ARG APP_ENV
ENV APP_ENV=${APP_ENV:-prod}

RUN if [ "$APP_ENV" = "prod" ]; then \
        cp /usr/local/etc/php/php.ini-production /usr/local/etc/php/php.ini && \
        docker-php-ext-install opcache && \
        echo "opcache.memory_consumption=256" >> /usr/local/etc/php/conf.d/opcache.ini && \
        echo "opcache.max_accelerated_files=20000" >> /usr/local/etc/php/conf.d/opcache.ini && \
        echo "opcache.validate_timestamps=0" >> /usr/local/etc/php/conf.d/opcache.ini && \
        echo "realpath_cache_size=4096K" >> /usr/local/etc/php/conf.d/opcache.ini && \
        echo "realpath_cache_ttl=600" >> /usr/local/etc/php/conf.d/opcache.ini; \
    else \
        apk add --update --no-cache libzip-dev && \
        docker-php-ext-configure zip && \
        docker-php-ext-install zip && \
        cp /usr/local/etc/php/php.ini-development /usr/local/etc/php/php.ini && \
        echo "[xdebug]" >> /usr/local/etc/php/php.ini && \
        echo "zend_extension=xdebug.so" >> /usr/local/etc/php/php.ini && \
        echo "xdebug.mode=debug" >> /usr/local/etc/php/php.ini && \
        echo "xdebug.start_with_request=yes" >> /usr/local/etc/php/php.ini && \
        echo "xdebug.discover_client_host=true" >> /usr/local/etc/php/php.ini && \
        echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/php.ini; \
    fi

COPY --from=composer:2 /usr/bin/composer /usr/bin/composer
