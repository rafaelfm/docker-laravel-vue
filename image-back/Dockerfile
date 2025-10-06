# Use a imagem oficial do PHP mais recente
FROM php:8.4-fpm
# Instala dependências do sistema
RUN apt-get update && apt-get install -y \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    unzip \
    git \
    zip \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd \
    && docker-php-ext-install pdo pdo_mysql

# Instala o Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Define o diretório de trabalho
WORKDIR /var/www

# Instala as dependências do Composer
#RUN composer install

# Expondo a porta 9000
EXPOSE 9000

# Comando para iniciar o PHP-FPM
CMD ["php-fpm"]