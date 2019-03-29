FROM php:7.3-fpm

RUN mkdir -p /code

ADD ./install_composer.sh /code/

WORKDIR /code

RUN apt-get -y -qq update \
    && apt-get -y -qq upgrade \
    && apt-get install -y -qq nginx wget zip unzip apt-utils libpng-dev libjpeg62-turbo-dev \
    && groupadd -r composer \
    && useradd --no-log-init -r -g composer composer \
    && chown -R composer /code \
    && mkdir -p /home/composer/ \
    && chown -R composer /home/composer \
    && ./install_composer.sh \
    && docker-php-ext-configure gd --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install pdo_mysql gd

USER composer


COPY composer.json /code
COPY composer.lock /code

RUN php composer.phar --quiet install --no-scripts --no-autoloader

ADD . /code
ADD ./nginx.conf /etc/nginx/nginx.conf

# Install a second time to ensure scripts run
RUN php composer.phar --quiet install

USER root

RUN chown -R www-data:www-data /code/storage /code/bootstrap/cache /code/vendor \
    && chmod -R 755 /code/storage /code/bootstrap/cache /code/vendor

ENTRYPOINT ["/bin/bash", "-c", "./entrypoint.sh"]
