FROM saschaegerer/ubuntu:16.04
ARG DEBIAN_FRONTEND=noninteractive

ENV PHP_VERSION=7.2
RUN set -xe && \
    apt-get update && \
    buildDependencies=software-properties-common && \
    apt-get install --no-install-recommends -y $buildDependencies curl  && \
    curl https://packagecloud.io/gpg.key | apt-key add - && \
    echo "deb http://packages.blackfire.io/debian any main" | tee /etc/apt/sources.list.d/blackfire.list && \
    add-apt-repository ppa:ondrej/php && \
    apt-get update && \
    apt-get install --no-install-recommends -y libfreetype6-dev libxml2-dev libcurl4-openssl-dev libsqlite3-dev libssl-dev libpcre3-dev libpng-dev libjpeg-dev libzip-dev libedit-dev ghostscript curl xz-utils binutils blackfire-php php7.2-fpm php7.2-xdebug php7.2-mbstring php7.2-gd php7.2-soap php7.2-zip && \
    apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false $buildDependencies && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY bin/* /usr/local/bin/
COPY my_custom.ini /etc/php/7.2/mods-available/
COPY php-fpm.conf /etc/php/7.2/fpm/pool.d/z_custom.conf

RUN set -xe && \
    phpenmod my_custom && \
    mkdir -p /var/www /run/php && \
    # make the blackfire and xdebug config file writeable for www-data.
    # this is a bit insecure but ok in that case as this image should only be used in development
    chown -R www-data:www-data /var/www /run/php /etc/php/7.2/mods-available/blackfire.ini /etc/php/7.2/mods-available/xdebug.ini && \
    echo "xdebug.max_nesting_level=1500" >> /etc/php/7.2/mods-available/xdebug.ini && \
    printf 'blackfire.agent_socket="${BLACKFIRE_SOCKET}"\n' >> /etc/php/7.2/mods-available/blackfire.ini && \
    echo "include=/etc/php/7.2/fpm/pool.d/*.conf" >> /etc/php/7.2/fpm/php-fpm.conf

ENTRYPOINT ["tini", "php-entrypoint", "--"]

WORKDIR /var/www
USER www-data

EXPOSE 9000
CMD ["php-fpm7.2"]
