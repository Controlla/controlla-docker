#
#--------------------------------------------------------------------------
# Image Setup
#--------------------------------------------------------------------------
#
FROM php:7.2-fpm

LABEL mainteners="Ivan Sotelo <isotelo@controlla.com.mx>,Gabriel Delgado <gdelgado@controlla.com.mx>"

# Create a non-root user
RUN useradd -ms /bin/bash controlla

# Install dependencies and extensions
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
    && php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer \
    && php -r "unlink('composer-setup.php');" \
    #####################################
    # Composer:
    #####################################
    # Install composer and add its bin to the PATH.
    && curl -s http://getcomposer.org/installer | php \
    && echo "export PATH=${PATH}:/var/www/vendor/bin" >> ~/.bashrc \
    && mv composer.phar /usr/local/bin/composer

# Set the working directory
WORKDIR /var/www/html
