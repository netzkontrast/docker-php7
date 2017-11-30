FROM phpdockerio/php71-fpm:latest
MAINTAINER Michael Schimmer <michael@netzkontrast.de>


# Install selected extensions and other stuff
RUN apt-get update \
    && apt-get -y --no-install-recommends install  php-memcached php7.1-mysql php7.1-gd php-imagick php7.1-intl php7.1-mbstring php-msgpack php-ssh2 php7.1-tidy php7.1-xsl php-xdebug php-zmq \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/*