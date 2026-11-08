FROM php:7.4.1-apache

# Konfigurasi arsip Debian Buster untuk PHP 7.4
RUN echo 'Acquire::Check-Valid-Until "false";' > /etc/apt/apt.conf.d/99no-check \
    && echo 'Acquire::https::Verify-Peer "false";' >> /etc/apt/apt.conf.d/99no-check \
    && echo 'Acquire::https::Verify-Host "false";' >> /etc/apt/apt.conf.d/99no-check \
    && echo "deb https://archive.debian.org/debian buster main" > /etc/apt/sources.list

# Install dependensi sistem, unixodbc, dan Microsoft ODBC Driver 17
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    gnupg2 \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libicu-dev \
    libzip-dev \
    unzip \
    unixodbc-dev \
    default-mysql-client \
    iputils-ping \
    libcap2-bin \
    && setcap cap_net_raw+ep /bin/ping \
    && curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add - \
    && curl https://packages.microsoft.com/config/debian/10/prod.list > /etc/apt/sources.list.d/mssql-release.list \
    && apt-get update \
    && ACCEPT_EULA=Y apt-get install -y msodbcsql17 \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd intl mysqli pdo_mysql zip bcmath \
    && pecl install sqlsrv-5.10.0 pdo_sqlsrv-5.10.0 \
    && docker-php-ext-enable sqlsrv pdo_sqlsrv \
    && rm -rf /var/lib/apt/lists/*

# Aktifkan Apache rewrite module
RUN a2enmod rewrite \
    && sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf