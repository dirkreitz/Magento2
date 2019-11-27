FROM php:7.3-apache-stretch

# Surpresses debconf complaints of trying to install apt packages interactively
# https://github.com/moby/moby/issues/4032#issuecomment-192327844
 
ARG DEBIAN_FRONTEND=noninteractive

# Update
RUN apt-get -y update --fix-missing && \
    apt-get upgrade -y && \
    apt-get --no-install-recommends install -y apt-utils && \
    rm -rf /var/lib/apt/lists/*

ENV PHP_UPLOAD_MAX_FILESIZE 20M
ENV PHP_POST_MAX_SIZE 100M
ENV PHP_MEMORY_LIMIT 2G

# Install useful tools and install important libaries
RUN apt-get -y update && \
    apt-get -y --no-install-recommends install mc nano wget dialog libsqlite3-dev libsqlite3-0 libssl-dev && \
    apt-get -y --no-install-recommends install mysql-client zlib1g-dev libzip-dev libicu-dev libxml2-dev libxslt-dev && \
    apt-get -y --no-install-recommends install --fix-missing apt-utils build-essential git curl && \ 
    apt-get -y --no-install-recommends install --fix-missing libcurl3 libcurl3-dev zip unzip openssl openssh-server && \
    rm -rf /var/lib/apt/lists/* && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install redis
RUN pecl install redis-5.1.1 && \
    docker-php-ext-enable redis

# Other PHP7 Extensions for Magento2
RUN docker-php-ext-install pdo_mysql && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install sockets && \
    docker-php-ext-install zip && \
    docker-php-ext-install -j$(nproc) intl && \
    docker-php-ext-install opcache && \
    docker-php-ext-install bcmath && \
    docker-php-ext-install soap && \
    docker-php-ext-install xsl

# Install xdebug
RUN pecl install xdebug-2.8.0 && \
    docker-php-ext-enable xdebug

# Install Freetype 
RUN apt-get -y update && \
    apt-get --no-install-recommends install -y libfreetype6-dev libjpeg62-turbo-dev libpng-dev && \
    rm -rf /var/lib/apt/lists/* && \
    docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && \
    docker-php-ext-install -j$(nproc) gd

# config to enable .htaccess
ADD apache_default /etc/apache2/sites-available/000-default.conf

# Enable apache modules
RUN a2enmod rewrite headers
RUN service apache2 restart

# For openssh
#RUN mkdir -p /var/run/sshd
#RUN echo "root:admin123" | chpasswd
#RUN sed -i 's/PermitRootLogin without-password/PermitRootLogin yes/' /etc/ssh/sshd_config
#RUN sed -i '$a\PermitRootLogin yes' /etc/ssh/ssh_config

# Cleanup
RUN rm -rf /usr/src/*

# Get Magento files
RUN rm -fr /var/www/html
RUN git clone https://github.com/magento/magento2.git /app/
RUN chmod -R o+w /app/pub /app/var
RUN chmod o+w /app/app/etc
RUN cd /app && /usr/local/bin/php -r "readfile('https://getcomposer.org/installer');" | /usr/local/bin/php
RUN cd /app && /usr/local/bin/php composer.phar install
RUN mkdir -p /app && rm -fr /var/www/html && ln -s /app /var/www/html
EXPOSE 80 3306 22