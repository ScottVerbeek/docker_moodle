FROM php:7.4-apache-buster

ARG DEBIAN_FRONTEND=noninteractive

ENV BUILD_PACKAGES="gettext gnupg libcurl4-openssl-dev libfreetype6-dev libicu-dev libjpeg62-turbo-dev \
                    libldap2-dev libmariadbclient-dev libmemcached-dev libpng-dev libpq-dev libxml2-dev libxslt-dev \
                    unixodbc-dev uuid-dev"

ENV PACKAGES_LDAP="libldap-2.4-2"

ENV PACKAGES_MEMCACHED="libmemcached11 libmemcachedutil2"

ENV PACKAGES_MYMARIA="libmariadb3"

ENV PACKAGES_PHP="intl mysqli opcache pgsql soap xsl xmlrpc"

ENV PACKAGES_POSTGRES="libpq5"

ENV PACKAGES_RUNTIME="ghostscript libaio1 libcurl4 libgss3 libicu63 libmcrypt-dev libxml2 libxslt1.1 \
  libzip-dev locales sassc unixodbc unzip zip"

ENV PROGRAMS="vim git"

# Install libraries, packages and programs.
RUN apt-get update \
    && apt-get install -y --no-install-recommends apt-transport-https \
    ${BUILD_PACKAGES} \
    ${PACKAGES_LDAP} \
    ${PACKAGES_MEMCACHED} \
    ${PACKAGES_MYMARIA} \
    ${PACKAGES_POSTGRES} \
    ${PACKAGES_RUNTIME} \
    ${PROGRAMS}

# Generate the locales configuration for both Australia
RUN echo 'en_AU.UTF-8 UTF-8' > /etc/locale.gen && locale-gen

# Install PHP packages.
RUN docker-php-ext-install ${PACKAGES_PHP}

# GD.
RUN docker-php-ext-configure gd --with-freetype=/usr/include/ --with-jpeg=/usr/include/ \
    && docker-php-ext-install gd

# LDAP.
RUN docker-php-ext-configure ldap --with-libdir=lib/x86_64-linux-gnu/ \
    && docker-php-ext-install ldap

# Memcached, MongoDB, Redis, APCu, igbinary, solr, uuid, xDebug.
RUN pecl install memcached mongodb redis apcu igbinary solr uuid xdebug \
    && docker-php-ext-enable memcached mongodb redis apcu igbinary solr uuid xdebug

# ZIP
RUN docker-php-ext-configure zip \
    && docker-php-ext-install zip

# Image clean up, keeps size down.
RUN pecl clear-cache \
    && apt-get remove --purge -y $BUILD_PACKAGES \
    && apt-get autoremove -y \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Create dataroots.
RUN mkdir /var/www/moodledata && chmod 0777 /var/www/moodledata \
    && mkdir /var/www/phpunitdata && chmod 0777 /var/www/phpunitdata \
    && mkdir /var/www/behatdata && chmod 0777 /var/www/behatdata

COPY php.ini /usr/local/etc/php/php.ini
COPY xdebug.ini /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini
COPY mailhog.conf /etc/apache2/conf-enabled/apache2_mailhog.conf

WORKDIR /var/www/html