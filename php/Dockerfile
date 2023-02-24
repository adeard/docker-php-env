FROM php:8.1.16-fpm
# WORKDIR /usr/share/nginx/html

RUN apt-get update && apt-get install -y \
        freetds-bin \
        freetds-dev \
        freetds-common \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libpng-dev \
        libzip-dev \
        libicu-dev \
        zip \
        libcurl4-openssl-dev pkg-config libssl-dev vim \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-configure intl \
    && docker-php-ext-install -j$(nproc) gd mysqli pdo pdo_mysql opcache dba gettext pcntl pdo_dblib zip intl bcmath 

# Configure MongoDB.
RUN bash -c '[[ -n "$(pecl list | grep mongodb)" ]] || (pecl install mongodb && docker-php-ext-enable mongodb)'

 # Configure LDAP.
RUN apt-get update && apt-get install libldap2-dev -y \
 && rm -rf /var/lib/apt/lists/* \
 && docker-php-ext-configure ldap --with-libdir=lib/$(gcc --dumpmachine)/ \
 && docker-php-ext-install ldap

# Configure ODBC
RUN apt-get update && apt-get install unixodbc unixodbc-dev -y \
    && docker-php-ext-configure pdo_odbc --with-pdo-odbc=unixODBC,/usr \
    && docker-php-ext-install pdo_odbc

# Configure AMQP
RUN apt-get update && apt-get install -y librabbitmq-dev libssh-dev \
    && docker-php-ext-install opcache bcmath sockets 
RUN bash -c '[[ -n "$(pecl list | grep amqp)" ]] || (pecl install amqp && docker-php-ext-enable amqp)'

# Configure Postgre PDO
RUN apt-get update && apt-get install -y libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql

# COPY php/opcache.ini /usr/local/etc/php/conf.d/opcache.ini
COPY ./data/ /var/www/html/

# RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
