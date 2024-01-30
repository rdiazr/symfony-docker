# From
FROM php:8.3-fpm-alpine

# Set timezone Europe/Madrid
RUN apk add --no-cache tzdata
ENV TZ=Europe/Madrid
#RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Install non-dev dependencies
RUN set -eux \
  && apk add --no-cache \
  git zip unzip bash curl tzdata icu-libs \
  c-client make gmp gettext \
  libintl libxslt libpng libwebp libjpeg-turbo freetype \
  oniguruma libxslt libpq vips \
  gmp libzip libxml2 freetds

# Install dependencies
RUN set -eux \
  && apk add --no-cache --virtual .build_deps \
  libpng-dev libwebp-dev libjpeg-turbo-dev freetype-dev \
  linux-headers oniguruma-dev libxslt-dev vips-dev \
  gmp-dev libzip-dev libxml2-dev freetds-dev \
  # PHPIZE_DEPS => 'autoconf 		dpkg-dev dpkg 		file 		g++ 		gcc 		libc-dev 		make 		pkgconf 		re2c'
  $PHPIZE_DEPS \
  \
  # Php extensions
  && docker-php-ext-install \
  mysqli \
  pdo_mysql \
  bcmath \
  mbstring \
  xml \
  gd \
  exif \
  zip \
  soap \
  intl \
  xsl \
  pcntl \
  sockets \
  sysvmsg \
  sysvsem \
  sysvshm \
  opcache \
  gmp \
  \
  # Install xdebug
  # && pecl install -o -f xdebug \
  # && docker-php-ext-enable xdebug \
  # \
  # Install apcu
  && pecl install -o -f apcu \
  && docker-php-ext-enable apcu \
  \
  # Clean php extensions
  && rm -rf /tmp/pear \
  && docker-php-source delete \
  \
  # Clean up \
  && apk del --purge .build_deps \
  && rm -rf /var/cache/apk/*

# Install composer
COPY --from=composer:latest /usr/bin/composer /usr/local/bin/composer

ARG SYMFONY_CLI_VERSION=5.8.5
RUN wget https://github.com/symfony-cli/symfony-cli/releases/download/v$SYMFONY_CLI_VERSION/symfony-cli_linux_amd64.tar.gz \
 && tar -xvzf symfony-cli_linux_amd64.tar.gz \
 && chmod +x symfony \
 && mv symfony /usr/local/bin/

# Copy php settings PHP_INI_DIR => '/usr/local/etc/php'
COPY ./php.ini ${PHP_INI_DIR}/conf.d/99-php.ini

# Usuario Linux-1000 Mac-501
ARG USER_NAME=gmao
ARG UID=501

RUN adduser --disabled-password --gecos "" --uid $UID  $USER_NAME

# Workdir
WORKDIR /var/www/html
USER $USER_NAME

# Expose port
EXPOSE 9000