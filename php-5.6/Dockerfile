FROM saschaegerer/ubuntu:16.04
ARG DEBIAN_FRONTEND=noninteractive

ENV PHP_VERSION=5.6
RUN set -xe && \
    apt-get update && \
    buildDependencies="\
        software-properties-common \
        build-essential \
        php-pear \
    " && \
    apt-get install --no-install-recommends -y $buildDependencies curl && \
    curl -s https://packagecloud.io/gpg.key | apt-key add - && \
    echo "deb http://packages.blackfire.io/debian any main" | tee /etc/apt/sources.list.d/blackfire.list && \
    add-apt-repository ppa:ondrej/php && \
    apt-get update && \
    buildDependencies="$buildDependencies\
        php5.6-dev \
    " && \
    apt-get install --no-install-recommends -y $buildDependencies libfreetype6-dev libxml2-dev libcurl4-openssl-dev libsqlite3-dev libssl-dev libpcre3-dev libpng-dev libjpeg-dev libzip-dev libedit-dev ghostscript curl xz-utils binutils blackfire-php php5.6-fpm php5.6-mbstring php5.6-gd php5.6-soap php5.6-zip php5.6-xml && \
    pecl update-channels && \
    pecl channel-update pecl.php.net && \
    pecl install xdebug && \
    pecl clear-cache && \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDependencies && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY bin/* /usr/local/bin/
COPY my_custom.ini /etc/php/5.6/mods-available/
COPY php-fpm.conf /etc/php/5.6/fpm/pool.d/z_custom.conf

RUN set -xe && \
    mkdir -p /var/www /run/php && \
    phpenmod my_custom && \
    echo "zend_extension=xdebug.so\nxdebug.max_nesting_level=1500" >> /etc/php/5.6/mods-available/xdebug.ini && \
    # make the blackfire and xdebug config file writeable for www-data.
    # this is a bit insecure but ok in that case as this image should only be used in development
    chown -R www-data:www-data /var/www /run/php /etc/php/5.6/mods-available/blackfire.ini /etc/php/5.6/mods-available/xdebug.ini && \
    printf 'blackfire.agent_socket="${BLACKFIRE_SOCKET}"\n' >> /etc/php/5.6/mods-available/blackfire.ini && \
    echo "include=/etc/php/5.6/fpm/pool.d/*.conf" >> /etc/php/5.6/fpm/php-fpm.conf

ENTRYPOINT ["tini", "php-entrypoint", "--"]

WORKDIR /var/www
USER www-data

EXPOSE 9000
CMD ["php-fpm5.6"]
