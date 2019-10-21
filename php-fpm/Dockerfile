FROM php:7.3.6-fpm

ENV TIMEZONE Asia/Shanghai
ENV SRC_DIR /home
ENV PHP_DIR /usr/local
ENV INIT_FILE /usr/local/etc/php/conf.d
ENV PHPREDIS_VERSION 3.1.6
ENV RABBITMQ_VERSION 0.5.2
ENV AMQP_VERSION 1.9.3
ENV MONGODB_VERSION 1.5.1

# replace sources.list
RUN sed -i 's/deb.debian.org/mirrors.163.com/g' /etc/apt/sources.list \
    && sed -i 's/security.debian.org/mirrors.163.com/g' /etc/apt/sources.list \
    && cp /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
    && echo "${TIMEZONE}" > /etc/timezone

# init
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    curl \
    vim \
    net-tools \
    wget \
    zip \
    git \
    unzip \
    procps \
    libz-dev \
    libzip-dev \
    libpq-dev \
    libjpeg-dev \
    libpng-dev \
    libfreetype6-dev \
    libssl-dev \
    libmcrypt-dev \
    libmemcached-dev \
    apt-utils \
    && rm -rf /var/lib/apt/lists/*

RUN apt-get update && apt-get install -y libxslt1-dev

# install php extentions
RUN pecl channel-update pecl.php.net \
    && docker-php-ext-configure gd \
    --enable-gd-native-ttf \
    --with-jpeg-dir=/usr/lib \
    --with-freetype-dir=/usr/include/freetype2 \
    && docker-php-ext-install -j$(nproc) bcmath mysqli pdo_mysql gd zip opcache pcntl sockets xsl

RUN pecl install xdebug \ 
    && docker-php-ext-enable xdebug

RUN apt-get install -y \
    memcached \
    && pecl install mongodb && docker-php-ext-enable mongodb \
    && pecl install redis && docker-php-ext-enable redis


#   rabbitmq-c
ADD install/rabbitmq-c-${RABBITMQ_VERSION}.tar.gz ${SRC_DIR}/
RUN cd ${SRC_DIR}/rabbitmq-c-${RABBITMQ_VERSION} \
    && ./configure --prefix=/usr/local/rabbitmq \
    && make \
    && make install \
    && rm -f ${SRC_DIR}/rabbitmq-c-${RABBITMQ_VERSION}.tar.gz \
    && rm -rf ${SRC_DIR}/rabbitmq-c-${RABBITMQ_VERSION}

RUN apt-get install -y \
        librabbitmq-dev \
        libssh-dev \
    && docker-php-ext-install \
        bcmath \
        sockets \
    && pecl install amqp \
    && docker-php-ext-enable amqp

RUN pecl install mcrypt-1.0.2 \
    && docker-php-ext-enable mcrypt

RUN chown -R www-data:www-data /var/www/html