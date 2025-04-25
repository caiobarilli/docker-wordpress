FROM alpine:latest

LABEL maintainer="Caio Barilli <caiobarilli@gmail.com>"
LABEL description="Docker image for Wordpress"

WORKDIR /var/www/html/

# Essentials
RUN echo "UTC" > /etc/timezone
RUN apk add --no-cache zip unzip curl sqlite nginx supervisor

# Installing bash
RUN apk add bash
RUN sed -i 's/bin\/ash/bin\/bash/g' /etc/passwd

# Updating
RUN apk update && apk upgrade

# Installing PHP
RUN apk --no-cache add \
    php83 \
    php83-fpm \
    php83-opcache \
    php83-pecl-apcu \
    php83-mysqli \
    php83-pgsql \
    php83-json \
    php83-openssl \
    php83-curl \
    php83-zlib \
    php83-soap \
    php83-xml \
    php83-fileinfo \
    php83-phar \
    php83-intl \
    php83-dom \
    php83-xmlwriter \
    php83-xmlreader \
    php83-ctype \
    php83-session \
    php83-iconv \
    php83-tokenizer \
    php83-zip \
    php83-simplexml \
    php83-mbstring \
    php83-mysqli \
    php83-pdo \
    php83-pdo_mysql \
    php83-gd \
    php83-sqlite3 \
    php83-redis \
    php83-posix \
    php83-pcntl \
    php83-bcmath \
    php83-gmp \
    php83-pdo_sqlite

# Installing composer
RUN curl -sS https://getcomposer.org/installer -o composer-setup.php
RUN php83 composer-setup.php --install-dir=/usr/local/bin --filename=composer
RUN rm -rf composer-setup.php

# Installing WP-CLI
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar && \
    php83 wp-cli.phar --info && \
    chmod +x wp-cli.phar && \
    mv wp-cli.phar /usr/local/bin/wp && \
    ln -s /usr/local/bin/wp /bin/wp

# Configure supervisor
RUN mkdir -p /etc/supervisor.d/
RUN mkdir -p /var/log/supervisor

# Creating group and user for Supervisor
RUN addgroup -S supervisor && adduser -S supervisor -G supervisor

# Creating directory and setting correct permissions
RUN mkdir -p /var/log/supervisor && chown -R supervisor:supervisor /var/log/supervisor

# Creating and adjusting Supervisor log permissions
RUN touch /var/log/supervisor/supervisord.log
RUN chown -R supervisor:supervisor /var/log/supervisor
RUN chmod 664 /var/log/supervisor/supervisord.log

# Copy supervisor configuration
COPY ./supervisord.ini /etc/supervisor.d/supervisord.ini

# Create user and group for supervisor process
RUN chown -R supervisor:supervisor /var/log/supervisor /etc/supervisor.d

# Configure PHP
RUN mkdir -p /run/php/
RUN touch /run/php/php8.3-fpm.pid
RUN chown -R supervisor:supervisor /run/php/

COPY ./php.ini /etc/php83/php.ini
COPY ./conf/php-fpm.conf /etc/php83/php-fpm.conf
COPY ./conf/www.conf /etc/php83/php-fpm.d/www.conf

# Criando diretório para os certificados SSL
RUN mkdir -p /etc/nginx/ssl

# Gerando certificado SSL autoassinado
RUN apk add --no-cache openssl && \
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -keyout /etc/nginx/ssl/nginx.key \
    -out /etc/nginx/ssl/nginx.crt \
    -subj "/C=BR/ST=SaoPaulo/L=SaoPaulo/O=Wordpress/CN=site.wordpress.test" \
    -addext "subjectAltName = DNS:site.wordpress.test"

# Configure nginx
COPY ./conf/nginx.conf /etc/nginx/
COPY ./conf/nginx-php.conf /etc/nginx/modules/

RUN mkdir -p /var/lib/nginx/logs/ \
    && mkdir -p /var/lib/nginx/tmp/client_body \
    && mkdir -p /run/nginx/

RUN touch /run/nginx/nginx.pid

# Adjust permissions for nginx log and temp directories
RUN chown -R nginx:nginx /var/lib/nginx /etc/nginx /run/nginx /run/nginx/nginx.pid /var/log/nginx
RUN chmod -R 775 /var/lib/nginx/tmp /var/lib/nginx/logs /run/nginx/

# Create symlinks for logs
RUN ln -sf /dev/stdout /var/log/nginx/access.log \
    && ln -sf /dev/stderr /var/log/nginx/error.log

# Ensure PHP has permissions to temp directories
RUN mkdir -p /var/lib/php/sessions /tmp/uploads \
    && chown -R nginx:nginx /var/lib/php/sessions /tmp/uploads \
    && chmod -R 775 /var/lib/php/sessions /tmp/uploads

# Building process
COPY . .

EXPOSE 80 443

# Run supervisord
CMD ["supervisord", "-c", "/etc/supervisor.d/supervisord.ini"]
