FROM php:7.2-fpm

RUN apt-get update && apt-get -qq install -y \
    apt-utils \
    git \
    curl \
    unzip \
    vim \
    nano \
    libzip-dev \
    libmcrypt-dev \
    libicu-dev \
    libxml2-dev libxslt1-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    mariadb-client \
    msmtp \
    mailutils \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-configure hash --with-mhash \
    && docker-php-ext-install -j$(nproc) intl xsl gd zip pdo_mysql opcache soap bcmath json iconv pcntl sockets

# Install xdebug extension
RUN pecl install xdebug-2.6.1 && docker-php-ext-enable xdebug \
    && echo "xdebug.default_enable = 1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.idekey = PHPSTORM" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_enable = 1" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_host = host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
    && echo "xdebug.remote_port = 9000" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# Configure MailHog
RUN echo 'sendmail_path = "/usr/bin/msmtp -t"' > /usr/local/etc/php/conf.d/mail.ini \
    && echo "SMTP = mail" > /usr/local/etc/php/conf.d/mail.ini \
    && echo "smtp_port = 1025" > /usr/local/etc/php/conf.d/mail.ini

# Install Composer
RUN cd /usr/src && curl -sS http://getcomposer.org/installer | php
RUN cd /usr/src && mv composer.phar /usr/bin/composer

# Install n98-magerun2
RUN cd /usr/local/bin && curl -sS -O https://files.magerun.net/n98-magerun2-latest.phar
RUN mv /usr/local/bin/n98-magerun2-latest.phar /usr/local/bin/n98-magerun2.phar
RUN chmod +x /usr/local/bin/n98-magerun2.phar

# Add https://github.com/hirak/prestissimo support
RUN composer global require hirak/prestissimo

WORKDIR /var/www/magento

CMD php-fpm
