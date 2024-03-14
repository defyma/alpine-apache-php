FROM alpine:3.18.3

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
  && php -r "if (hash_file('sha384', 'composer-setup.php') === 'dac665fdc30fdd8ec78b38b9800061b4150413ff2e3b6f88543c636f7cd84f6db9189d43a81e5503cda447da73c7e5b6') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
  && php composer-setup.php \
  && php -r "unlink('composer-setup.php');" \
  && chmod +x composer.phar \
  && mv composer.phar /usr/local/bin/composer

#ADD docker-entrypoint.sh /
#HEALTHCHECK CMD wget -q --no-cache --spider localhost

# Change Server Admin, Name, Document Root
RUN sed -i "s/ServerAdmin\ you@example.com/ServerAdmin\ ${SERVER_ADMIN}/" /etc/apache2/httpd.conf
RUN sed -i "s/#ServerName\ www.example.com:80/ServerName\ ${HTTP_SERVER_NAME}/" /etc/apache2/httpd.conf
RUN sed -i 's#^DocumentRoot ".*#DocumentRoot "/var/www/html/public"#g' /etc/apache2/httpd.conf
RUN sed -i 's#Directory "/var/www/localhost/htdocs"#Directory "/var/www/html/public"#g' /etc/apache2/httpd.conf
RUN sed -i 's#AllowOverride None#AllowOverride All#' /etc/apache2/httpd.conf

# Change TransferLog after ErrorLog
RUN sed -i 's#^ErrorLog .*#ErrorLog "/dev/stderr"\nTransferLog "/dev/stdout"#g' /etc/apache2/httpd.conf
RUN sed -i 's#CustomLog .* combined#CustomLog "/dev/stdout" combined#g' /etc/apache2/httpd.conf

# SSL DocumentRoot and Log locations
RUN sed -i 's#^ErrorLog .*#ErrorLog "/dev/stderr"#g' /etc/apache2/conf.d/ssl.conf
RUN sed -i 's#^TransferLog .*#TransferLog "/dev/stdout"#g' /etc/apache2/conf.d/ssl.conf
RUN sed -i 's#^DocumentRoot ".*#DocumentRoot "/var/www/html/public"#g' /etc/apache2/conf.d/ssl.conf
RUN sed -i "s/ServerAdmin\ you@example.com/ServerAdmin\ ${SERVER_ADMIN}/" /etc/apache2/conf.d/ssl.conf
RUN sed -i "s/ServerName\ www.example.com:443/ServerName\ ${HTTPS_SERVER_NAME}/" /etc/apache2/conf.d/ssl.conf

# Re-define LogLevel
RUN sed -i "s#^LogLevel .*#LogLevel ${LOG_LEVEL}#g" /etc/apache2/httpd.conf

# Enable commonly used apache modules
RUN sed -i 's/#LoadModule\ rewrite_module/LoadModule\ rewrite_module/' /etc/apache2/httpd.conf
RUN sed -i 's/#LoadModule\ deflate_module/LoadModule\ deflate_module/' /etc/apache2/httpd.conf
RUN sed -i 's/#LoadModule\ expires_module/LoadModule\ expires_module/' /etc/apache2/httpd.conf

# Modify php memory limit and timezone
RUN sed -i "s/memory_limit = .*/memory_limit = ${PHP_MEMORY_LIMIT}/" /etc/php82/php.ini
RUN sed -i "s#^;date.timezone =\$#date.timezone = \"${TZ}\"#" /etc/php82/php.ini

CMD ["httpd", "-D", "FOREGROUND"]
