FROM php:7.4-apache

ARG BUILD_APP=false

# Install xdebug
# RUN yes | pecl install xdebug \
# 	&& echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" \
# 		 > /usr/local/etc/php/conf.d/xdebug.ini \
# 	&& echo "xdebug.remote_enable=on" >> /usr/local/etc/php/conf.d/xdebug.ini \
# 	&& echo "xdebug.remote_autostart=on" >> /usr/local/etc/php/conf.d/xdebug.ini

RUN apt-get update

# Install git, process tools
RUN apt-get -y install \ 
	git \ 
	procps \
    zip \
    curl \
    sudo \
    unzip \
    libicu-dev \
    libbz2-dev \
    libpng-dev \
    libjpeg-dev \
    libmcrypt-dev \
    libreadline-dev \
    libfreetype6-dev \
	libzip-dev \
	libonig-dev \
    g++

# .htaccess mod_rewrite for URL rewrite
RUN a2enmod rewrite headers

# PHP extensions
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"
RUN docker-php-ext-install \
    bz2 \
    intl \
    iconv \
    bcmath \
    opcache \
    calendar \
    mbstring \
    pdo_mysql \
    zip

RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd

# Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN composer self-update --snapshot

# Install NodeJS
ARG NODE_VERSION=14.11.0
ARG NVM_DIR=/usr/local/nvm

# https://github.com/creationix/nvm#install-script
RUN mkdir $NVM_DIR && curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.35.3/install.sh | bash

# add node and npm to path so the commands are available
ENV NODE_PATH $NVM_DIR/v$NODE_VERSION/lib/node_modules
ENV PATH $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

RUN node -v
RUN npm -v

# Clean up
RUN apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# Build App
COPY . /app

RUN rm -rf /var/www/html
RUN ln -s /app/public /var/www/html

WORKDIR /app
RUN composer install
# RUN npm ci

# App
WORKDIR /app/public/
RUN rm -rf /var/www/html
RUN ln -s /app/public /var/www/html

# Permission
RUN chmod 755 /var/www/html