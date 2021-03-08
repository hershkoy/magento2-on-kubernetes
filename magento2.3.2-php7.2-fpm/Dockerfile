FROM php:7.2-fpm

# Install dependencies
RUN apt-get update \
  && apt-get install -y \
    libfreetype6-dev \ 
    libicu-dev \ 
    libjpeg62-turbo-dev \ 
    libmcrypt-dev \ 
    libpng-dev \ 
    libxslt1-dev \ 
    sendmail-bin \ 
    sendmail \ 
    sudo \ 
    cron \ 
    rsyslog \ 
    default-mysql-client \ 
    git \
	unzip

# Configure the gd library
RUN docker-php-ext-configure \
  gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

# Install required PHP extensions

RUN docker-php-ext-install \
  dom \ 
  gd \ 
  intl \ 
  mbstring \ 
  pdo_mysql \ 
  xsl \ 
  zip \ 
  soap \ 
  bcmath \ 
  pcntl \ 
  sockets

# Install Xdebug (but don't enable)
RUN pecl install -o -f xdebug

ENV PHP_MEMORY_LIMIT 2G
ENV PHP_ENABLE_XDEBUG false
ENV MAGENTO_ROOT /var/www/html

ENV DEBUG false
ENV UPDATE_UID_GID false

ADD etc/php-xdebug.ini /usr/local/etc/php/conf.d/zz-xdebug-settings.ini
ADD etc/mail.ini /usr/local/etc/php/conf.d/zz-mail.ini

ENV MAGENTO_RUN_MODE developer
ENV UPLOAD_MAX_FILESIZE 64M

ENV COMPOSER_ALLOW_SUPERUSER 1
ENV COMPOSER_GITHUB_TOKEN ""
ENV COMPOSER_MAGENTO_USERNAME ""
ENV COMPOSER_MAGENTO_PASSWORD ""
ENV COMPOSER_BITBUCKET_KEY ""
ENV COMPOSER_BITBUCKET_SECRET ""

ARG M2SETUP_VERSION=2.3.3
ARG M2SETUP_INSTALL_DB=true
ARG M2SETUP_USE_SAMPLE_DATA=true

ENV M2SETUP_VERSION=$M2SETUP_VERSION
ENV M2SETUP_INSTALL_DB=$M2SETUP_INSTALL_DB
ENV M2SETUP_USE_SAMPLE_DATA=$M2SETUP_USE_SAMPLE_DATA

VOLUME /root/.composer/cache

ADD etc/php-cli.ini /usr/local/etc/php/conf.d/zz-magento.ini

# Get composer installed to /usr/local/bin/composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Install n98-magerun2.phar and move to /usr/local/bin/
RUN curl -O https://files.magerun.net/n98-magerun2.phar && chmod +x ./n98-magerun2.phar && mv ./n98-magerun2.phar /usr/local/bin/

# Install magedbm2.phar and move to /usr/local/bin
RUN curl -LO https://s3.eu-west-2.amazonaws.com/magedbm2-releases/magedbm2.phar && chmod +x ./magedbm2.phar && mv ./magedbm2.phar /usr/local/bin

# Install mageconfigsync and move to /usr/local/bin
RUN curl -L https://github.com/punkstar/mageconfigsync/releases/download/0.5.0-beta.1/mageconfigsync-0.5.0-beta.1.phar > mageconfigsync.phar && chmod +x ./mageconfigsync.phar && mv ./mageconfigsync.phar /usr/local/bin

ADD bin/* /usr/local/bin/
ADD etc/php-fpm.ini /usr/local/etc/php/conf.d/zz-magento.ini
ADD etc/php-fpm.conf /usr/local/etc/
ADD docker-entrypoint.sh /docker-entrypoint.sh

RUN ["chmod", "+x", "/usr/local/bin/magento-installer"]
RUN ["chmod", "+x", "/usr/local/bin/magento-command"]
RUN ["chmod", "+x", "/usr/local/bin/magerun2"]
RUN ["chmod", "+x", "/usr/local/bin/run-cron"]
RUN ["chmod", "+x", "/docker-entrypoint.sh"]

ENTRYPOINT ["/docker-entrypoint.sh"]

ADD composer.json composer.json

CMD ["php-fpm", "-F"]
