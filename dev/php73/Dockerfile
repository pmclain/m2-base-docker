FROM php:7.3-fpm

LABEL MAINTAINER="Patrick McLain <pat@pmclain.com>"

# Install dependencies
RUN apt-get update \
  && apt-get install -y \
    libfreetype6-dev \ 
    libicu-dev \ 
    libjpeg62-turbo-dev \ 
    libsodium-dev \
    libzip-dev \
    libpng-dev \ 
    libxslt1-dev \
    openssl \
    sudo \
    cron \
    rsyslog \
    mysql-client \
    git \
    ssmtp \
    unzip \
    procps

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
  sodium

RUN pecl install -o -f xdebug-2.7.0beta1

VOLUME /root/.composer/cache
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

ENV PHP_MEMORY_LIMIT 756M
ENV XDEBUG_REMOTE_HOST docker.for.mac.localhost
ENV MAGENTO_ROOT /var/www/html
ENV MAGENTO_CLEAN_DATABASE 0
ENV XDEBUG_ENABLE false

ADD etc/php-fpm.ini /usr/local/etc/php/conf.d/zz-magento.ini
ADD etc/php-xdebug.ini /usr/local/etc/php/conf.d/zz-xdebug.ini
RUN echo "sendmail_path = /usr/sbin/ssmtp -t" > /usr/local/etc/php/conf.d/zz-sendmail.ini
RUN echo "mailhub=mailcatcher:25\nUseTLS=NO\nFromLineOverride=YES" > /etc/ssmtp/ssmtp.conf

RUN version="73" \
    && curl -A "Docker" -o /tmp/blackfire-probe.tar.gz -D - -L -s https://blackfire.io/api/v1/releases/probe/php/linux/amd64/$version \
    && mkdir -p /tmp/blackfire \
    && tar zxpf /tmp/blackfire-probe.tar.gz -C /tmp/blackfire \
    && mv /tmp/blackfire/blackfire-*.so /usr/local/lib/php/extensions/no-debug-non-zts-20180731/blackfire.so \
    && printf "extension=blackfire.so\nblackfire.agent_socket=tcp://blackfire:8707\n" > /usr/local/etc/php/conf.d/blackfire.ini \
    && rm -rf /tmp/blackfire /tmp/blackfire-probe.tar.gz

RUN mkdir -p /tmp/blackfire \
    && curl -A "Docker" -L https://blackfire.io/api/v1/releases/client/linux_static/amd64 | tar zxp -C /tmp/blackfire \
    && mv /tmp/blackfire/blackfire /usr/bin/blackfire \
    && rm -Rf /tmp/blackfire

RUN curl -sSLO https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz \
  && tar xzf ioncube_loaders_lin_x86-64.tar.gz \
  && mv ioncube/ioncube_loader_lin_7.3.so /usr/local/lib/php/extensions/no-debug-non-zts-20180731/ioncube.so \
  && rm ioncube_loaders_lin_x86-64.tar.gz ioncube/ -rf \
  && printf "zend_extension=ioncube.so" > /usr/local/etc/php/conf.d/blackfire.ini

ADD bin/* /usr/local/bin/

RUN ["chmod", "+x", "/usr/local/bin/magento"]
RUN ["chmod", "+x", "/usr/local/bin/magento-installer"]
RUN ["chmod", "+x", "/usr/local/bin/magento-sample-data"]

ADD docker-entrypoint.sh /docker-entrypoint.sh

RUN ["chmod", "+x", "/docker-entrypoint.sh"]

ENTRYPOINT ["/docker-entrypoint.sh"]

ADD etc/php-fpm.conf /usr/local/etc/

CMD ["php-fpm", "-F", "-R"]