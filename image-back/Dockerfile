FROM php:8.2-fpm-bullseye
WORKDIR /var/www/html

RUN apt-get update && apt-get install -y --no-install-recommends \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libwebp-dev \
    libzip-dev \
    libxml2-dev \
    libxslt-dev \
    libcurl4-openssl-dev \
    libonig-dev \
    libssh-dev \
    libmagic-dev \
    libkrb5-dev \
    libpq-dev \
    postgresql-client \
    libtool \
    unzip \
    zip \
    git \
    curl \
    nano \
    vim \
    htop \
    locales \
    procps \
    iputils-ping \
    build-essential \
    software-properties-common \
    apt-transport-https \
    gnupg \
    net-tools \
    lsof \
    jpegoptim optipng pngquant gifsicle \
    && docker-php-ext-configure gd --with-freetype --with-jpeg --with-webp \
    && docker-php-ext-install -j$(nproc) gd pdo pdo_mysql pdo_pgsql zip exif pcntl mbstring xml curl dom xsl \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# XDEBUG (somente para desenvolvimento)
COPY docker-laravel-vue/image-back/docker-php-ext-xdebug.ini $PHP_INI_DIR/conf.d/docker-php-ext-xdebug.ini

RUN pecl install xdebug && docker-php-ext-enable xdebug

# CRIAÇÃO DE USUÁRIOS
RUN groupadd -g 1000 www && useradd -u 1000 -ms /bin/bash -g www www

RUN usermod -aG sudo www-data && echo "www-data ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# COMPOSER
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# LARAVEL INSTALLER
RUN composer global require laravel/installer

# Remove storage/framework/testing/disks se existir
RUN rm -rf /var/www/html/storage/framework/testing/disks || true

# CONFIGURAÇÕES DO PHP-FPM
RUN sed -i 's/127.0.0.1:9000/0.0.0.0:9000/g' /usr/local/etc/php-fpm.d/www.conf && \
    sed -i 's/;clear_env = no/clear_env = no/g' /usr/local/etc/php-fpm.d/www.conf && \
    sed -i 's/^user = www-data/user = www/' /usr/local/etc/php-fpm.d/www.conf && \
    sed -i 's/^group = www-data/group = www/' /usr/local/etc/php-fpm.d/www.conf

# PERMISSÕES DO LARAVEL
RUN mkdir -p /var/www/html/storage/logs \
    /var/www/html/storage/framework/cache \
    /var/www/html/storage/framework/sessions \
    /var/www/html/storage/framework/views \
    /var/www/html/bootstrap/cache && \
    chown -R www-data:www-data /var/www/html && \
    chmod -R 777 /var/www/html/storage /var/www/html/bootstrap/cache /var/www/html/storage/logs

# INSTALA DEPENDÊNCIAS FRONTEND (como www-data)
USER www-data 
COPY --chown=www-data:www-data back /var/www/html

# RETORNA AO ROOT PARA AJUSTES FINAIS
USER root
RUN chown -R www-data:www-data /var/www/html/node_modules && \
    find /var/www/html/node_modules -type d -exec chmod 755 {} + && \
    find /var/www/html/node_modules -type f -exec chmod 644 {} + && \
    find /var/www/html/node_modules/.bin -type f -exec chmod +x {} \; 2>/dev/null || true && \
    chmod -R 775 /var/www/html/node_modules/.vite-temp || true

# VALIDA .env
RUN if [ ! -f /var/www/html/.env ]; then \
      echo 'ERRO: Arquivo .env não encontrado!'; exit 1; \
    fi && \
    if ! grep -qE '^APP_ENV=(local|production)' /var/www/html/.env; then \
      echo 'ERRO: APP_ENV não está definido corretamente no .env!'; exit 1; \
    fi

# CONFIGURAÇÕES FINAIS
USER www-data
CMD ["php-fpm"]
