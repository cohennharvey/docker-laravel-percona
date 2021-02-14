FROM php:7.4-fpm
LABEL maintainer="Cohen Harvey <cohen.n.harvey@gmail.com>"

ARG XDEBUG_HOST="host.docker.internal"
ARG XDEBUG_CONF="disabled"

ENV LANGUAGE   en_US.UTF-8
ENV LANG       en_US.UTF-8
ENV LC_ALL     en_US.UTF-8
ENV COMPOSER_ALLOW_SUPERUSER 1

# Ensure UTF-8
RUN DEBIAN_FRONTEND="noninteractive" apt-get clean && apt-get update
RUN DEBIAN_FRONTEND="noninteractive" apt-get install -y apt-utils locales
RUN echo "LC_ALL=en_US.UTF-8" >> /etc/environment
RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen
RUN echo "LANG=en_US.UTF-8" > /etc/locale.
RUN DEBIAN_FRONTEND="noninteractive" locale-gen en_US.UTF-8

# Install system dependencies
RUN apt-get update && apt-get install -y \
    vim \
    curl \
    wget \
    build-essential \
    git \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libzip-dev \
    zip \
    unzip \
    libpq-dev

# Install node
RUN DEBIAN_FRONTEND="noninteractive" curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN DEBIAN_FRONTEND="noninteractive" apt-get install -y nodejs

# Install PHP extensions
RUN docker-php-ext-install -j$(nproc) opcache bcmath zip pdo_mysql mysqli
#    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
#    && docker-php-ext-install -j$(nproc) gd

# Install XDEBUG
RUN pecl install xdebug
RUN mkdir -p /usr/local/etc/php/${XDEBUG_CONF}
RUN \
    echo "zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20190902/xdebug.so" > /usr/local/etc/php/${XDEBUG_CONF}/xdebug.ini && \
    echo "xdebug.mode = debug" >> /usr/local/etc/php/${XDEBUG_CONF}/xdebug.ini && \
    echo "xdebug.start_with_request = trigger" >> /usr/local/etc/php/${XDEBUG_CONF}/xdebug.ini && \
    echo "xdebug.discover_client_host = 0" >> /usr/local/etc/php/${XDEBUG_CONF}/xdebug.ini && \
    echo "xdebug.client_port = 9003" >> /usr/local/etc/php/${XDEBUG_CONF}/xdebug.ini && \
    echo "xdebug.client_host =" ${XDEBUG_HOST} >> /usr/local/etc/php/${XDEBUG_CONF}/xdebug.ini && \
    echo "xdebug.output_dir=\"/tmp\"" >> /usr/local/etc/php/${XDEBUG_CONF}/xdebug.ini

# Install composer
COPY --from=composer:2.0 /usr/bin/composer /usr/bin/composer

##############################################################################
# phpunit
##############################################################################

ENV PHPUNIT_VERSION 9.4.2
RUN mkdir -p /root/src \
    && cd /root/src \
    && wget https://phar.phpunit.de/phpunit-${PHPUNIT_VERSION}.phar \
    && chmod +x phpunit-${PHPUNIT_VERSION}.phar \
    && mv phpunit-${PHPUNIT_VERSION}.phar /usr/local/bin/phpunit \
    && rm -rf /root/src \
    && phpunit --version


COPY ./resources/config/log.conf /usr/local/etc/php-fpm.d/zz-log.conf

COPY ./resources/config/php.ini-development /usr/local/etc/php/php.ini

# Clean up
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

EXPOSE 3000
EXPOSE 3001
