FROM php:8.3.36-apache

# Install dependensi sistem, unixodbc, dan Microsoft ODBC Driver
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
    # 1. Unduh kuncinya dan simpan di keyring
    && curl -fsSL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor -o /usr/share/keyrings/microsoft-prod.gpg \
    # 2. Tambahkan repo Debian 12 (bookworm) secara eksplisit menunjuk [signed-by=...]
    && echo "deb [arch=amd64,arm64,armhf signed-by=/usr/share/keyrings/microsoft-prod.gpg] https://packages.microsoft.com/debian/12/prod bookworm main" > /etc/apt/sources.list.d/mssql-release.list \
    # 3. Update dan install msodbcsql17 (atau msodbcsql18)
    && apt-get update \
    && ACCEPT_EULA=Y apt-get install -y msodbcsql17 \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd intl mysqli pdo_mysql zip \
    && pecl install sqlsrv pdo_sqlsrv \
    && docker-php-ext-enable sqlsrv pdo_sqlsrv \
    && rm -rf /var/lib/apt/lists/*

# Aktifkan modul rewrite Apache dan izinkan override .htaccess
RUN a2enmod rewrite \
    && sed -i '/<Directory \/var\/www\/>/,/<\/Directory>/ s/AllowOverride None/AllowOverride All/' /etc/apache2/apache2.conf