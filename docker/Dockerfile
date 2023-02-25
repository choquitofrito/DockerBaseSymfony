FROM php:8.1-apache

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions  intl

RUN apt-get update && apt-get install -y libpq-dev && docker-php-ext-install pdo pdo_pgsql pgsql

# RUN sed -i '/SSLCertificateFile.*snakeoil\.pem/c\SSLCertificateFile \/etc\/ssl\/certs\/mycert.crt' /etc/apache2/sites-available/default-ssl.conf
#RUN sed -i '/SSLCertificateKeyFile.*snakeoil\.key/cSSLCertificateKeyFile /etc/ssl/private/mycert.key\' /etc/apache2/sites-available/default-ssl.conf

COPY ./docker/*.crt /etc/ssl/certs/
COPY ./docker/*.key /etc/ssl/private/

# COPY ./docker/symfony-ssl.conf /etc/apache2/sites-enabled/symfony-ssl.conf

COPY ./docker/apache-selfsigned.crt /etc/ssl/certs/apache-selfsigned.crt
COPY ./docker/apache-selfsigned.key /etc/ssl/private/apache-selfsigned.key


RUN a2enmod rewrite && a2enmod ssl && a2enmod socache_shmcb
#RUN a2ensite default-ssl
#RUN a2ensite symfony-ssl

RUN apt-get update && apt-get upgrade -y



RUN curl -sSk https://getcomposer.org/installer | php -- --disable-tls && \
   mv composer.phar /usr/local/bin/composer

RUN apt update && apt install -yqq nodejs npm

COPY . /var/www/

COPY ./docker/apache.conf /etc/apache2/sites-available/000-default.conf

RUN cd /var/www && \
    composer install && \
    npm install --force && \
    npm run build

    
WORKDIR /var/www/

ENTRYPOINT ["bash", "./docker/docker.sh"]

EXPOSE 80
