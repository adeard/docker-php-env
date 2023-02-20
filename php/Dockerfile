FROM php:8.1.16-fpm
WORKDIR /usr/share/nginx/html

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
    && docker-php-ext-install -j$(nproc) gd

# Configure MongoDB.
# RUN bash -c '[[ -z "$(pecl list | grep mongodb)" ]] && (pecl install mongodb && docker-php-ext-enable mongodb)'

RUN bash -c '[[ -n "$(pecl list | grep mongodb)" ]] || (pecl install mongodb && docker-php-ext-enable mongodb)'

# RUN mkdir -p /usr/src/php/ext/mongodb \
#     && curl -fsSL https://pecl.php.net/get/mongodb | tar xvz -C "/usr/src/php/ext/mongodb" --strip 1 \
#     && docker-php-ext-install mongodb

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

# RUN bash -c '[[ -z "$(pecl list | grep amqp)" ]] && (pecl install amqp && docker-php-ext-enable amqp)'

RUN bash -c '[[ -n "$(pecl list | grep amqp)" ]] || (pecl install amqp && docker-php-ext-enable amqp)'

# Configure Postgre PDO
RUN apt-get update && apt-get install -y libpq-dev \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-install pdo pdo_pgsql pgsql

RUN docker-php-ext-install mysqli pdo pdo_mysql opcache dba gettext pcntl pdo_dblib zip
RUN docker-php-ext-configure intl && docker-php-ext-install intl bcmath 
COPY php/opcache.ini /usr/local/etc/php/conf.d/opcache.ini
COPY ./data/ /var/www/html/

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
