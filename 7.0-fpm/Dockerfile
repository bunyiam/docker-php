FROM dockette/jessie

MAINTAINER Milan Sulc <sulcmil@gmail.com>

# PHP
ENV PHP_MODS_DIR=/etc/php/7.0/mods-available
ENV PHP_CLI_DIR=/etc/php/7.0/cli
ENV PHP_CLI_CONF_DIR=${PHP_CLI_DIR}/conf.d
ENV PHP_CGI_DIR=/etc/php/7.0/cgi
ENV PHP_CGI_CONF_DIR=${PHP_CGI_DIR}/conf.d
ENV PHP_FPM_DIR=/etc/php/7.0/fpm
ENV PHP_FPM_CONF_DIR=${PHP_FPM_DIR}/conf.d
ENV PHP_FPM_POOL_DIR=${PHP_FPM_DIR}/pool.d
ENV TZ=America/Los_Angeles

# INSTALLATION
RUN apt-get update && apt-get dist-upgrade -y && \
    # DEPENDENCIES #############################################################
    apt-get install -y wget curl apt-transport-https ca-certificates lsb-release patch make && \
    # PHP DEB.SURY.CZ ##########################################################
    wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
    curl -sL https://deb.nodesource.com/setup_8.x | bash - && \
    echo "deb https://packages.sury.org/php/ jessie main" > /etc/apt/sources.list.d/php.list && \
    sh -c 'echo "deb http://opensource.wandisco.com/debian `lsb_release -cs` svn19" >> /etc/apt/sources.list.d/subversion18.list' && \
    wget -q http://opensource.wandisco.com/wandisco-debian.gpg -O- | apt-key add - && \
    apt-get update && \
    apt-get install -y git subversion libsvn-dev mysql-client rsync nodejs && \
    apt-get install -y --no-install-recommends \
        php7.0-apc \
        php7.0-apcu \
        php7.0-bcmath \
        php7.0-bz2 \
        php7.0-calendar \
        php7.0-cgi \
        php7.0-cli \
        php7.0-ctype \
        php7.0-curl \
        php7.0-dev    \
        php7.0-fpm \
        php7.0-geoip \
        php7.0-gettext \
        php7.0-gd \
        php7.0-intl \
        php7.0-imagick \
        php7.0-imap \
        php7.0-ldap \
        php7.0-mbstring \
        php7.0-mcrypt \
        php7.0-memcached \
        php7.0-mongo \
        php7.0-mysql \
        php7.0-pdo \
        php7.0-pgsql \
        php7.0-redis \
        php7.0-soap \
        php7.0-sqlite3 \
        php7.0-ssh2 \
        php7.0-zip \
        php7.0-xmlrpc \
        php7.0-xsl && \
    # COMPOSER #################################################################
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
    composer global require "hirak/prestissimo:^0.3" && \
    # Build PHP SVN ###############################################################
    svn co http://svn.php.net/repository/pecl/svn/trunk/ && \
    cd trunk && \
    svn up -r 343422 && \
    wget "https://bugs.php.net/patch-display.php?bug_id=75206&patch=php7again.diff&revision=1511799141&download=1" -O "php7.patch" && \
    patch -p0 -l < php7.patch && \
    phpize --clean && phpize && \
    ./configure && \
    make && make install && \
    # PHP MOD(s) ###############################################################
    ln -s ${PHP_MODS_DIR}/custom.ini ${PHP_CLI_CONF_DIR}/999-custom.ini && \
    ln -s ${PHP_MODS_DIR}/custom.ini ${PHP_CGI_CONF_DIR}/999-custom.ini && \
    ln -s ${PHP_MODS_DIR}/custom.ini ${PHP_FPM_CONF_DIR}/999-custom.ini && \
    # CLEAN UP #################################################################
    rm ${PHP_FPM_POOL_DIR}/www.conf && \
    apt-get clean -y && \
    apt-get autoclean -y && \
    apt-get remove -y wget curl && \
    apt-get autoremove -y && \
    rm -rf /var/lib/apt/lists/* /var/lib/log/* /tmp/* /var/tmp/*

# FILES (it overrides originals)
ADD conf.d/custom.ini ${PHP_MODS_DIR}/custom.ini
ADD fpm/php-fpm.conf ${PHP_FPM_DIR}/php-fpm.conf

# WORKDIR
WORKDIR /srv

# COMMAND
CMD ["php-fpm7.0"]
