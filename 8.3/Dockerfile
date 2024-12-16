FROM alpine:3.19.1

WORKDIR /var/www/html

# Set environment variables
ENV SERVER_ADMIN="you@example.com" \
    HTTP_SERVER_NAME="www.example.com" \
    HTTPS_SERVER_NAME="www.example.com" \
    LOG_LEVEL="info" \
    TZ="UTC" \
    PHP_MEMORY_LIMIT="256M"

# Setup apache and php
RUN apk --no-cache --update \
    add apache2 \
    apache2-ssl \
    curl \
    php83 \
    php83-dev \
    php83-common \
    php83-apache2 \
    php83-bcmath \
    php83-bz2 \
    php83-calendar \
    php83-common \
    php83-ctype \
    php83-curl \
    php83-dom \
    php83-gd \
    php83-iconv \
    php83-mbstring \
    php83-mysqli \
    php83-mysqlnd \
    php83-openssl \
    php83-pdo_mysql \
    php83-pdo_pgsql \
    php83-pdo_sqlite \
    php83-phar \
    php83-posix \
    php83-session \
    php83-opcache \
    php83-xml \
    php83-zip \
    php83-ldap \
    php83-gmp \
    php83-gd \
    php83-fileinfo \
    php83-exif \
    php83-ctype \
    php83-soap \
    php83-pcntl \
    php83-xmlreader \
    php83-xmlwriter \
    php83-xsl \
    php83-simplexml \
    php83-tokenizer \
    && mkdir -p /var/www/html

EXPOSE 80 443

RUN ln -s /usr/bin/php83 /usr/bin/php

# install composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
  && php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
  && php composer-setup.php \
  && php -r "unlink('composer-setup.php');" \
  && chmod +x composer.phar \
  && mv composer.phar /usr/local/bin/composer

#ADD docker-entrypoint.sh /

#HEALTHCHECK CMD wget -q --no-cache --spider localhost

#ENTRYPOINT ["/docker-entrypoint.sh"]

RUN echo 'Updating configurations'

# Change Server Admin, Name, Document Root
RUN sed -i "s/ServerAdmin\ you@example.com/ServerAdmin\ ${SERVER_ADMIN}/" /etc/apache2/httpd.conf
RUN sed -i "s/#ServerName\ www.example.com:80/ServerName\ ${HTTP_SERVER_NAME}/" /etc/apache2/httpd.conf
RUN sed -i 's#^DocumentRoot ".*#DocumentRoot "/var/www/html"#g' /etc/apache2/httpd.conf
RUN sed -i 's#Directory "/var/www/localhost/htdocs"#Directory "/var/www/html"#g' /etc/apache2/httpd.conf
RUN sed -i 's#AllowOverride None#AllowOverride All#' /etc/apache2/httpd.conf

# Change TransferLog after ErrorLog
RUN sed -i 's#^ErrorLog .*#ErrorLog "/dev/stderr"\nTransferLog "/dev/stdout"#g' /etc/apache2/httpd.conf
RUN sed -i 's#CustomLog .* combined#CustomLog "/dev/stdout" combined#g' /etc/apache2/httpd.conf

# SSL DocumentRoot and Log locations
RUN sed -i 's#^ErrorLog .*#ErrorLog "/dev/stderr"#g' /etc/apache2/conf.d/ssl.conf
RUN sed -i 's#^TransferLog .*#TransferLog "/dev/stdout"#g' /etc/apache2/conf.d/ssl.conf
RUN sed -i 's#^DocumentRoot ".*#DocumentRoot "/var/www/html"#g' /etc/apache2/conf.d/ssl.conf
RUN sed -i "s/ServerAdmin\ you@example.com/ServerAdmin\ ${SERVER_ADMIN}/" /etc/apache2/conf.d/ssl.conf
RUN sed -i "s/ServerName\ www.example.com:443/ServerName\ ${HTTPS_SERVER_NAME}/" /etc/apache2/conf.d/ssl.conf

# Re-define LogLevel
RUN sed -i "s#^LogLevel .*#LogLevel ${LOG_LEVEL}#g" /etc/apache2/httpd.conf

# Enable commonly used apache modules
RUN sed -i 's/#LoadModule\ rewrite_module/LoadModule\ rewrite_module/' /etc/apache2/httpd.conf
RUN sed -i 's/#LoadModule\ deflate_module/LoadModule\ deflate_module/' /etc/apache2/httpd.conf
RUN sed -i 's/#LoadModule\ expires_module/LoadModule\ expires_module/' /etc/apache2/httpd.conf

# Modify php memory limit and timezone
RUN sed -i "s/memory_limit = .*/memory_limit = ${PHP_MEMORY_LIMIT}/" /etc/php83/php.ini
RUN sed -i "s#^;date.timezone =\$#date.timezone = \"${TZ}\"#" /etc/php83/php.ini

RUN echo 'Running Apache'

CMD ["httpd", "-D", "FOREGROUND"]
