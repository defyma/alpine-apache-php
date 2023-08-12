FROM alpine:3.18.3

WORKDIR /var/www/html

# Setup apache and php
RUN apk --no-cache --update \
    add apache2 \
    apache2-ssl \
    curl \
    php82 \
    php82-dev \
    php82-common \
    php82-apache2 \
    php82-bcmath \
    php82-bz2 \
    php82-calendar \
    php82-common \
    php82-ctype \
    php82-curl \
    php82-dom \
    php82-gd \
    php82-iconv \
    php82-mbstring \
    php82-mysqli \
    php82-mysqlnd \
    php82-openssl \
    php82-pdo_mysql \
    php82-pdo_pgsql \
    php82-pdo_sqlite \
    php82-phar \
    php82-posix \
    php82-session \
    php82-opcache \
    php82-xml \
    php82-zip \
    php82-ldap \
    php82-gmp \
    php82-gd \
    php82-fileinfo \
    php82-exif \
    php82-ctype \
    php82-soap \
    php82-pcntl \
    php82-xmlreader \
    php82-xmlwriter \
    php82-xsl \
    php82-simplexml \
    php82-tokenizer \
    && mkdir -p /var/www/html/public

EXPOSE 80 443

RUN ln -s /usr/bin/php82 /usr/bin/php

# install composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php -r "if (hash_file('sha384', 'composer-setup.php') === 'e21205b207c3ff031906575712edab6f13eb0b361f2085f1f1237b7126d785e826a450292b6cfd1d64d92e6563bbde02') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
  && php composer-setup.php \
  && php -r "unlink('composer-setup.php');" \
  && chmod +x composer.phar \
  && mv composer.phar /usr/local/bin/composer

ADD docker-entrypoint.sh /

#HEALTHCHECK CMD wget -q --no-cache --spider localhost

ENTRYPOINT ["/docker-entrypoint.sh"]
