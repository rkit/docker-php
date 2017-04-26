FROM php:7.1.4-fpm

# --- SOFT --- #

RUN apt-get update && apt-get install -y \
    openssh-client \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng12-dev \
    zlib1g-dev \
    libssl-dev \
    g++ \
    libmemcached-dev \
    imagemagick \
    libmagickwand-6.q16-dev --no-install-recommends \
    wget \
    sendmail \
    git \
    ruby \
    nodejs \
    npm

RUN ln -s /usr/lib/x86_64-linux-gnu/ImageMagick-6.8.9/bin-Q16/MagickWand-config /usr/bin

RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ && \
    docker-php-ext-install gd && \
    docker-php-ext-install opcache && \
    docker-php-ext-install zip && \
    docker-php-ext-install pdo_mysql && \
    docker-php-ext-install mysqli && \
    docker-php-ext-install mbstring

RUN pecl channel-update pecl.php.net && \
    pecl install redis && \
    pecl install mongodb && \
    pecl install memcached && \
    pecl install xdebug && \
    pecl install imagick

RUN echo "extension=redis.so" > /usr/local/etc/php/conf.d/ext-redis.ini && \
    echo "extension=mongodb.so" > /usr/local/etc/php/conf.d/ext-mongodb.ini && \
    echo "extension=memcached.so" > /usr/local/etc/php/conf.d/ext-memcached.ini && \
    echo "extension=imagick.so" > /usr/local/etc/php/conf.d/ext-imagick.ini && \
    echo "zend_extension=xdebug.so" > /usr/local/etc/php/conf.d/ext-xdebug.ini

# --- COMPOSER --- #

RUN curl -sS https://getcomposer.org/installer | php
RUN mv composer.phar /usr/local/bin/composer

# --- ICU --- #

RUN apt-get install tzdata && \
    wget http://download.icu-project.org/files/icu4c/55.1/icu4c-55_1-src.tgz -O icu4c-55_1-src.tgz && \
    tar -zxvf icu4c-55_1-src.tgz && \
    mkdir -p build && \
    cd build && \
    CPPFLAGS="-DU_CHARSET_IS_UTF8=1" ../icu/source/runConfigureICU Linux && \
    make install && \
    apt-get install -y libicu-dev && \
    wget https://github.com/php/php-src/archive/php-7.1.4.tar.gz && \
    tar -xvf php-7.1.4.tar.gz && \
    cd php-src-php-7.1.4/ext/intl && \
    export LD_LIBRARY_PATH=/usr/local/lib/icu && \
    phpize && \
    ./configure --enable-intl --with-icu-dir=DEFAULT && \
    make && \
    make install && \
    echo "extension=intl.so" > /usr/local/etc/php/conf.d/ext-intl.ini && \
    rm -fr build

# --- PREPARE --- #

EXPOSE 9000

CMD ["php-fpm", "--allow-to-run-as-root"]
