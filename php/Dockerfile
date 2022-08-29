FROM php:8.1-fpm-buster

RUN echo "Acquire::http::Pipeline-Depth 0;" > /etc/apt/apt.conf.d/privet && echo "Acquire::http::No-Cache true;" >> /etc/apt/apt.conf.d/privet && echo "Acquire::BrokenProxy    true;" >> /etc/apt/apt.conf.d/privet

RUN apt-get update
RUN apt-get -qq install -y \
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
    mailutils \
    golang-go \
    && docker-php-ext-configure hash --with-mhash \
    && docker-php-ext-configure gd --with-jpeg --with-freetype \
    && docker-php-ext-install -j$(nproc) intl xsl gd zip pdo_mysql opcache soap bcmath iconv pcntl sockets

# Enable Xdebug
RUN pecl install xdebug && docker-php-ext-enable xdebug
RUN echo "xdebug.mode=develop,debug"  >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini \
&& echo "xdebug.client_host=host.docker.internal" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini

# instal source gardian
RUN PHP_VERSION=$(php -v | head -n1 | cut -d' ' -f2 | cut -d. -f1-2) \
    && mkdir -p /tmp/sourceguardian \
    && cd /tmp/sourceguardian \
    && curl -Os https://www.sourceguardian.com/loaders/download/loaders.linux-x86_64.tar.gz \
    && tar xzf loaders.linux-x86_64.tar.gz \
    && cp ixed.${PHP_VERSION}.lin "$(php -i | grep '^extension_dir =' | cut -d' ' -f3)/sourceguardian.so" \
    && echo "extension=sourceguardian.so" > /usr/local/etc/php/conf.d/15-sourceguardian.ini \
    && rm -rf /tmp/sourceguardian

#setup blackfire
RUN apt-get install -y gnupg
RUN curl -s https://packages.blackfire.io/gpg.key | apt-key add - \
  && echo "deb http://packages.blackfire.io/debian any main" | tee /etc/apt/sources.list.d/blackfire.list \
  && apt-get update \
  && apt-get install blackfire-agent blackfire-php

# instal source gardian
RUN PHP_VERSION=$(php -v | head -n1 | cut -d' ' -f2 | cut -d. -f1-2) \
    && mkdir -p /tmp/sourceguardian \
    && cd /tmp/sourceguardian \
    && curl -Os https://www.sourceguardian.com/loaders/download/loaders.linux-x86_64.tar.gz \
    && tar xzf loaders.linux-x86_64.tar.gz \
    && cp ixed.${PHP_VERSION}.lin "$(php -i | grep '^extension_dir =' | cut -d' ' -f3)/sourceguardian.so" \
    && echo "extension=sourceguardian.so" > /usr/local/etc/php/conf.d/15-sourceguardian.ini \
    && rm -rf /tmp/sourceguardian


# Configure MailHog
RUN go get github.com/mailhog/mhsendmail && cp /root/go/bin/mhsendmail /usr/local/bin/
RUN echo 'sendmail_path="/usr/local/bin/mhsendmail --smtp-addr=mailhog:1025"' >> /usr/local/etc/php/conf.d/mail.ini

# Set memory limit
RUN echo "memory_limit=4G" >> /usr/local/etc/php/conf.d/memory_limit.ini

# Install Composer
RUN cd /usr/src && curl https://getcomposer.org/download/2.1.14/composer.phar --output composer.phar | php
RUN cd /usr/src && mv composer.phar /usr/bin/composer
RUN chmod +x /usr/bin/composer

# Install n98-magerun2
RUN cd /usr/local/bin && curl -sS -O https://files.magerun.net/n98-magerun2-latest.phar
RUN mv /usr/local/bin/n98-magerun2-latest.phar /usr/local/bin/n98-magerun2.phar
RUN chmod +x /usr/local/bin/n98-magerun2.phar

WORKDIR /var/www/magento

CMD php-fpm
