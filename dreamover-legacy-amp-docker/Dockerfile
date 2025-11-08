FROM php:5.6.40-apache
LABEL maintainer="Owen Yip <me@owenyip.com>"

RUN docker-php-ext-install mysql
RUN docker-php-ext-install mysqli
RUN apt-get update && apt-get upgrade -y \
  && apt-get install -y \
  build-essential \
  g++ \
  libbz2-dev \
  libc-client-dev \
  libcurl4-gnutls-dev \
  libedit-dev \
  libfreetype6-dev \
  libicu-dev \
  libjpeg62-turbo-dev \
  libkrb5-dev \
  libldap2-dev \
  libldb-dev \
  libmagickwand-dev \
  libmcrypt-dev \
  libmemcached-dev \
  libpng-dev \
  libpq-dev \
  libsqlite3-dev \
  libssl-dev \
  libreadline-dev \
  libxslt1-dev \
  libzip-dev \
  libjpeg-dev \
  memcached \
  jpegoptim \
  optipng \
  pngquant \
  gifsicle \
  wget \
  unzip \
  zlib1g-dev \
  && docker-php-ext-install -j$(nproc) \
  bcmath \
  bz2 \
  calendar \
  exif \
  gettext \
  mysqli \
  opcache \
  pdo_mysql \
  pdo_pgsql \
  pgsql \
  soap \
  sockets \
  xmlrpc \
  xsl

COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN docker-php-ext-configure gd \
  --with-freetype-dir=/usr/include/freetype2 \
  --with-png-dir=/usr/include \
  --with-jpeg-dir=/usr/include \
  && docker-php-ext-install -j$(nproc) gd \
  # && PHP_OPENSSL=yes docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
  # && docker-php-ext-install -j$(nproc) imap \
  && docker-php-ext-configure intl \
  && docker-php-ext-install -j$(nproc) intl \
  && docker-php-ext-configure zip \
  && docker-php-ext-install zip \
  && docker-php-ext-install shmop sysvmsg sysvsem sysvshm \
  && pecl install mongo-1.6.1 && docker-php-ext-enable mongo \
  && pecl install apcu-4.0.11 && docker-php-ext-enable apcu \
  && yes '' | pecl install imagick && docker-php-ext-enable imagick

# Install wkhtmltopdf
RUN apt-get install -y --no-install-recommends libxrender1 libfontconfig libssl1.0-dev
ADD https://github.com/wkhtmltopdf/wkhtmltopdf/releases/download/0.12.4/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz /tmp
RUN tar xvJf /tmp/wkhtmltox-0.12.4_linux-generic-amd64.tar.xz -C /tmp
RUN cp /tmp/wkhtmltox/bin/wk* /usr/local/bin/

# Install lib xvfb-run pour la generation des pdf
RUN apt-get install -y --no-install-recommends xvfb xfonts-100dpi xfonts-75dpi xfonts-scalable xfonts-cyrillic xauth

RUN docker-php-source delete \
  && apt-get remove -y g++ wget \
  && apt-get autoremove --purge -y && apt-get autoclean -y && apt-get clean -y \
  && rm -rf /var/lib/apt/lists/* \
  && rm -rf /tmp/* /var/tmp/*

RUN mkdir -p  /etc/apache2/from-host

RUN echo "" >> /etc/apache2/apache2.conf \
  && echo "# Include the configurations from the host machine" >> /etc/apache2/apache2.conf \
  && echo "IncludeOptional from-host/*.conf" >> /etc/apache2/apache2.conf

# ARG uid
# RUN useradd -G www-data,root -u $uid -d /home/ec2-user ec2-user
# RUN mkdir -p /home/ec2-user/.composer && \
#     chown -R ec2-user:ec2-user /home/ec2-user
ADD 000-default.conf /etc/apache2/sites-enabled/
ADD default-ssl.conf /etc/apache2/sites-anabled/

RUN groupadd dev -g 888
RUN useradd dev -u 888 -g dev -d /home/dev -m
RUN echo '%dev ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER dev:dev