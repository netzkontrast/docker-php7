FROM php:7.1.12-cli-alpine3.4


ENV TERRAFORM_VERSION 0.11.1
ENV DOCKER_CHANNEL edge
ENV DOCKER_VERSION 17.11.0-ce
ENV AWS_CLI_VERSION 1.14.7

RUN apk add --no-cache \
    python \
    py-pip \
    ca-certificates \
    openssl \
    groff \
    less \
    bash \
    curl \
    jq \
    git \
    zip \
  && pip install --no-cache-dir --upgrade pip awscli==${AWS_CLI_VERSION} \
  && EXPECTED_COMPOSER_SIGNATURE=$(wget -q -O - https://composer.github.io/installer.sig) && \
      php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
      php -r "if (hash_file('SHA384', 'composer-setup.php') === '${EXPECTED_COMPOSER_SIGNATURE}') { echo 'Composer.phar Installer verified'; } else { echo 'Composer.phar Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" && \
      php composer-setup.php --install-dir=/usr/bin --filename=composer && \
      php -r "unlink('composer-setup.php');"  \
  && aws configure set preview.cloudfront true


RUN echo @testing http://nl.alpinelinux.org/alpine/edge/testing >> /etc/apk/repositories && \
#    sed -i -e "s/v3.4/edge/" /etc/apk/repositories && \
    echo /etc/apk/respositories && \
    apk update && \
    apk add --no-cache bash \
    openssh-client \
    wget \
    supervisor \
    libevent-dev \
    curl \
    curl-dev \
    libcurl \
    python-dev \
    py-pip \
    augeas-dev \
    openssl-dev \
    ca-certificates \
    dialog \
    autoconf \
    make \
    gcc \
    gettext-dev \
    gettext \
    musl-dev \
    linux-headers \
    libmcrypt-dev \
    libpng-dev \
    icu-dev \
    zeromq \
    libpq \
    libxslt-dev \
    libffi-dev \
    freetype-dev \
    sqlite-dev \
    libjpeg-turbo-dev && \
    docker-php-ext-configure gd \
      --with-gd \
      --with-freetype-dir=/usr/include/ \
      --with-png-dir=/usr/include/ \
      --with-jpeg-dir=/usr/include/ && \
    #curl iconv session
    docker-php-ext-install pdo_mysql pdo_sqlite mysqli mcrypt gd exif intl xsl json soap dom zip opcache curl pcntl gettext sockets && \
    pecl install xdebug && \
    pecl install mongodb && \
    docker-php-source delete && \
    pip install -U pip

RUN pecl install event

RUN apk add libzmq

RUN apk add zeromq-dev

RUN pecl install zmq-beta

RUN [ ! -e /etc/nsswitch.conf ] && echo 'hosts: files dns' > /etc/nsswitch.conf

ADD composer.json composer.json

RUN composer update

RUN set -ex; \
# why we use "curl" instead of "wget":
# + wget -O docker.tgz https://download.docker.com/linux/static/stable/x86_64/docker-17.03.1-ce.tgz
# Connecting to download.docker.com (54.230.87.253:443)
# wget: error getting response: Connection reset by peer
	apk add --no-cache --virtual .fetch-deps \
		curl \
		tar \
	; \
	\
# this "case" statement is generated via "update.sh"
	apkArch="$(apk --print-arch)"; \
	case "$apkArch" in \
		x86_64) dockerArch='x86_64' ;; \
		aarch64) dockerArch='aarch64' ;; \
		ppc64le) dockerArch='ppc64le' ;; \
		s390x) dockerArch='s390x' ;; \
		*) echo >&2 "error: unsupported architecture ($apkArch)"; exit 1 ;;\
	esac; \
	\
	if ! curl -fL -o docker.tgz "https://download.docker.com/linux/static/${DOCKER_CHANNEL}/${dockerArch}/docker-${DOCKER_VERSION}.tgz"; then \
		echo >&2 "error: failed to download 'docker-${DOCKER_VERSION}' from '${DOCKER_CHANNEL}' for '${dockerArch}'"; \
		exit 1; \
	fi; \
	\
	tar --extract \
		--file docker.tgz \
		--strip-components 1 \
		--directory /usr/local/bin/ \
	; \
	rm docker.tgz; \
	\
	apk del .fetch-deps; \
	\
	dockerd -v; \
	docker -v


RUN wget -O terraform.zip https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_amd64.zip && \
    unzip terraform.zip -d /usr/local/bin && \
    rm -f terraform.zip

ENTRYPOINT ["/bin/bash", "-c"]