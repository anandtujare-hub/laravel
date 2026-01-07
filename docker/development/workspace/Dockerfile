FROM php:8.4-cli

ARG UID=1000
ARG GID=1000
ARG NODE_VERSION=22
ARG XDEBUG_ENABLED=false

# -------------------------------------------------
# System dependencies
# -------------------------------------------------
RUN apt-get update && apt-get install -y --no-install-recommends \
    curl \
    unzip \
    git \
    sudo \
    ca-certificates \
    gnupg \
    libpq-dev \
    libonig-dev \
    libssl-dev \
    libxml2-dev \
    libcurl4-openssl-dev \
    libicu-dev \
    libzip-dev \
    && docker-php-ext-install -j$(nproc) \
        pdo_pgsql \
        pgsql \
        opcache \
        intl \
        zip \
        bcmath \
        soap

# -------------------------------------------------
# Node.js (system-wide, NO NVM)
# -------------------------------------------------
RUN curl -fsSL https://deb.nodesource.com/setup_${NODE_VERSION}.x | bash - \
    && apt-get install -y nodejs \
    && npm install -g npm@latest

# -------------------------------------------------
# PECL extensions
# -------------------------------------------------
RUN pecl install redis \
    && docker-php-ext-enable redis

RUN if [ "$XDEBUG_ENABLED" = "true" ]; then \
        pecl install xdebug && docker-php-ext-enable xdebug ; \
    fi

# -------------------------------------------------
# Composer
# -------------------------------------------------
RUN curl -sS https://getcomposer.org/installer | php -- \
    --install-dir=/usr/local/bin \
    --filename=composer

# -------------------------------------------------
# Cleanup
# -------------------------------------------------
RUN apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# -------------------------------------------------
# User
# -------------------------------------------------
RUN groupadd -g ${GID} www \
    && useradd -m -u ${UID} -g www -s /bin/bash www \
    && usermod -aG sudo www \
    && echo "www ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

USER www
WORKDIR /var/www

ENTRYPOINT []
CMD ["bash"]
