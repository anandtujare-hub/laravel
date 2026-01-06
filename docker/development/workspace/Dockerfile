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
        soap \
    && rm -rf /var/lib/apt/lists/*

# -------------------------------------------------
# PECL extensions (isolated for reliability)
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
# User setup (matches host UID/GID)
# -------------------------------------------------
RUN groupadd -g ${GID} www \
    && useradd -m -u ${UID} -g www -s /bin/bash www \
    && usermod -aG sudo www \
    && echo "www ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# -------------------------------------------------
# Switch user
# -------------------------------------------------
USER www
WORKDIR /var/www

# -------------------------------------------------
# NVM + Node.js
# -------------------------------------------------
RUN curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash \
    && export NVM_DIR="$HOME/.nvm" \
    && . "$NVM_DIR/nvm.sh" \
    && nvm install ${NODE_VERSION} \
    && nvm alias default ${NODE_VERSION}

RUN echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.bashrc \
    && echo '[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"' >> ~/.bashrc

ENTRYPOINT []
CMD ["bash"]
