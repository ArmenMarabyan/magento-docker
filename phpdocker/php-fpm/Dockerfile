FROM php:7.4-fpm-alpine

RUN apk --update add wget \
  curl \
  git \
  grep \
  build-base \
  libmemcached-dev \
  libmcrypt-dev \
  libxml2-dev \
  imagemagick-dev \
  pcre-dev \
  libtool \
  make \
  autoconf \
  g++ \
  cyrus-sasl-dev \
  libgsasl-dev \
  supervisor \
  freetype-dev \
  libjpeg-turbo-dev \
  libpng-dev \
  icu-dev \
  libzip-dev \
  libxslt-dev

RUN docker-php-ext-install mysqli pdo pdo_mysql tokenizer xml pcntl gd zip bcmath simplexml soap xsl sockets
RUN pecl channel-update pecl.php.net \
    && pecl install memcached \
    && pecl install imagick-beta \
    && pecl install mcrypt-1.0.3 \
    && docker-php-ext-enable memcached \
    && docker-php-ext-enable imagick \
    && docker-php-ext-enable mcrypt \
#    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/ \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
#    && docker-php-ext-install -j$(getconf _NPROCESSORS_ONLN) gd \
  	&& NPROC=$(grep -c ^processor /proc/cpuinfo 2>/dev/null || 1) \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-install exif \
    && docker-php-ext-configure intl \
    && docker-php-ext-install intl

# install MSSQL support and ODBC driver
#RUN wget https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/msodbcsql17_17.5.1.1-1_amd64.apk && \
#    wget https://download.microsoft.com/download/e/4/e/e4e67866-dffd-428c-aac7-8d28ddafb39b/mssql-tools_17.5.1.1-1_amd64.apk && \
#    apk add --allow-untrusted msodbcsql17_17.5.1.1-1_amd64.apk && \
#    apk add --allow-untrusted mssql-tools_17.5.1.1-1_amd64.apk && \
#    apk add --no-cache --virtual .phpize-deps $PHPIZE_DEPS unixodbc-dev && \
#    pecl install pdo_sqlsrv && \
#    docker-php-ext-enable pdo_sqlsrv && \
#    apk del .phpize-deps && \
#    rm msodbcsql17_17.5.1.1-1_amd64.apk && \
#    rm mssql-tools_17.5.1.1-1_amd64.apk



RUN rm /var/cache/apk/* && \
    mkdir -p /var/www

COPY run-supervisor.sh /etc/supervisor/run-supervisor.sh

RUN chmod 777 /etc/supervisor/run-supervisor.sh

# install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer --version=1.8.6

COPY supervisord.conf /etc/supervisord.conf
#start supervisor
#ENTRYPOINT ["/usr/bin/supervisord", "-n", "-c", "/etc/supervisord.conf"]