FROM netzkontrast/nginx
MAINTAINER Michael Schimmer <michael@netzkontrast.de>

# Fixes some weird terminal issues such as broken clear / CTRL+L
ENV TERM=linux

# Install Ondrej repos for Ubuntu Xenial, PHP7.1, composer and selected extensions
RUN export DEBIAN_FRONTEND=noninteractive \
    && echo "deb http://ppa.launchpad.net/ondrej/php/ubuntu xenial main" > /etc/apt/sources.list.d/ondrej-php.list \
    && echo "deb http://ppa.launchpad.net/ondrej/php-qa/ubuntu xenial main" > /etc/apt/sources.list.d/ondrej-php-qa.list \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 4F4EA0AAE5267A6C \
    && apt-get update \
    && apt-get -y --no-install-recommends install git curl ca-certificates \
        php7.1-cli php7.1-curl php-apcu php-apcu-bc php7.1-fpm \
        php-memcached php7.1-mysql php7.1-gd php-imagick \
        php7.1-intl php7.1-mbstring php-msgpack php-ssh2 \
        php7.1-tidy php7.1-xsl php-xdebug php-zmq \
        php7.1-json php-sodium php7.1-opcache php7.1-readline php7.1-xml php7.1-zip \
    && curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* ~/.composer


# PHP-FPM packages need a nudge to make them docker-friendly
COPY overrides.conf /etc/php/7.1/fpm/pool.d/z-overrides.conf

# PHP-FPM has really dirty logs, certainly not good for dockerising
# The following startup script contains some magic to clean these up
COPY php-fpm-startup /usr/bin/php-fpm
CMD /usr/bin/php-fpm

# Open up fcgi port
EXPOSE 9000