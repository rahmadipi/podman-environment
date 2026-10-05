FROM php:7.4.1-apache

# Bypass ISP HTTP port 80 blocks and expired container SSL certs
RUN echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check \
    && echo 'Acquire::https::Verify-Peer "false";' >> /etc/apt/apt.conf.d/99no-check \
    && echo 'Acquire::https::Verify-Host "false";' >> /etc/apt/apt.conf.d/99no-check \
    && echo "deb https://archive.debian.org/debian buster main" > /etc/apt/sources.list

# Install packages and mysqldump
RUN apt-get update && apt-get install -y --no-install-recommends \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libicu-dev \
    libzip-dev \
    unzip \
    default-mysql-client \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd intl mysqli pdo_mysql zip \
    && rm -rf /var/lib/apt/lists/*

# Enable Apache rewrite module and htaccess overrides
RUN a2enmod rewrite \
    && sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf