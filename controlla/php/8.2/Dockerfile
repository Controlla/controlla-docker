# Use the official PHP 8.2 image as the base image
FROM php:8.2-fpm

# Set the maintainer label
LABEL maintainer="Ivan Sotelo <isotelo@controlla.com.mx>, Gabriel Delgado <gdelgado@controlla.com.mx>"

# Create a non-root user
RUN useradd -ms /bin/bash controlla

# Install system dependencies and PHP extensions in one step
RUN apt-get update && apt-get install -y \
    zsh \
    unzip \
    git \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libzip-dev \
    libexif-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) gd zip exif pdo_mysql bcmath

# Install Composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');"

# Add Composer binary to the PATH
ENV PATH="${PATH}:/var/www/vendor/bin"

# Install NVM
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash

# Install Node.js version 14.17.6
RUN /bin/bash -c "source /root/.nvm/nvm.sh && nvm install 14.17.6"

# Set the working directory
WORKDIR /var/www/html
