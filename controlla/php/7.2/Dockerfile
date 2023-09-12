# Use the official PHP 7.2 image as the base image
FROM php:7.2-fpm

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
    && rm -rf /var/lib/apt/lists/* 

# Create the directory for Composer installation and set permissions
USER controlla
RUN mkdir -p /home/controlla/.composer \
    && chmod -R 755 /home/controlla/.composer  # Corrected permissions to 755

# Install Composer for the 'controlla' user
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php --install-dir=/home/controlla/.composer --filename=composer \
    && php -r "unlink('composer-setup.php');"

# Set the working directory
WORKDIR /var/www/html

# Add Composer bin directory to PATH
ENV PATH="/home/controlla/.composer/vendor/bin:${PATH}"

# Switch back to the root user
USER root

# Install NVM for the 'controlla' user
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | su - controlla -c "bash"

# Install Node.js version 14.17.6 for the 'controlla' user
USER controlla
RUN /bin/bash -c "source /home/controlla/.nvm/nvm.sh && nvm install 14.17.6"

# Switch back to the root user
USER root
