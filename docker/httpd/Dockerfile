FROM php:8.3.3-apache-bullseye as app

WORKDIR /var/www/html

# Define a build-time variables
ARG USERNAME
ARG GID
ARG UID

# Copy the virtual host configuration file to the sites-available directory
COPY ./conf/mywebsite_vhost.conf /etc/apache2/sites-available/mywebsite_vhost.conf

# Copy typo3 php necessary config
COPY conf/extra.ini.template /usr/local/etc/php/conf.d/extra.ini

## PHP extension installer image, https://github.com/mlocati/docker-php-extension-installer
COPY --from=mlocati/php-extension-installer /usr/bin/install-php-extensions /usr/local/bin/

## Install system dependencies
RUN apt-get update \
   && apt-get install -y vim curl

## The following is based on typo3 System Requirements
# Install typo3  nesseccery php extensions
RUN set -eux; \
   install-php-extensions pdo_mysql intl zip @composer

# activate apache modules
RUN a2enmod rewrite \
   && a2enmod expires \
   && a2enmod headers

# Create a user with UID 1000 and GID 1000
RUN groupadd -g ${GID} ${USERNAME} \
   && useradd -m -r -u ${UID} -g ${USERNAME} ${USERNAME}

# Enable the site
RUN a2ensite mywebsite_vhost.conf

FROM app as app_dev

COPY ./conf/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini

# Install xdebug
RUN set -eux; \
	install-php-extensions xdebug \

#  linking logs to stdout and stderr  NEED TO BE CHECKED
#RUN ln -sfT /dev/stderr "/var/log/apache2/error.log" && \
#    ln -sfT /dev/stdout "/var/log/apache2/access.log"
