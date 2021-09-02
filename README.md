# wb15-docker-debug

## Resource límits
 
>Pregunta 1 : Como hemos comentado en clase alguna vez, cuando estemos en producción siempre vamos a settear resource límits en nuestros contenedores. Configura el entorno que lanzaste ayer para que no superen el uso de 2 cpu y 2 de ram cada nodo.

## Problema magento

> Pregunta 2 : Arregla el problema plantenado en clase. Documenta todo el proceso.
```
docker run  --cpus $i --rm -ti -v /run/mysqld/mysqld.sock:/run/mysqld/mysqld.sock -v /var/www/magento2/:/var/www/magento2/ -v /var/run/redis/redis.sock:/var/run/redis/redis.sock  magento sudo -u www-data php /var/www/magento2/bin/magento setup:cron:run
```

Dockerfile:
```
FROM debian:buster
# PHP
ENV PHP_MODS_DIR=/etc/php/7.1/mods-available
ENV PHP_CLI_DIR=/etc/php/7.1/cli
ENV PHP_CLI_CONF_DIR=${PHP_CLI_DIR}/conf.d
ENV PHP_CGI_DIR=/etc/php/7.1/cgi
ENV PHP_CGI_CONF_DIR=${PHP_CGI_DIR}/conf.d
ENV TZ=Europe/Madrid
# INSTALLATION
RUN apt update && apt dist-upgrade -y && \
# DEPENDENCIES #############################################################
apt install -y wget curl apt-transport-https ca-certificates git unzip && \
# PHP DEB.SURY.CZ ##########################################################
wget -O /etc/apt/trusted.gpg.d/php.gpg https://packages.sury.org/php/apt.gpg && \
echo "deb https://packages.sury.org/php/ buster main" > /etc/apt/sources.list.d/php.list && \
apt update && \
apt install -y --no-install-recommends \
sudo php7.1-fpm php7.1-fpm php7.1-xml php7.1-ctype php7.1-gd php7.1-dom php7.1-simplexml php7.1-mcrypt php7.1-bcmath php7.1-curl php7.1-iconv php7.1-intl php7.1-xsl php7.1-mbstring php7.1-zip php7.1-zip php7.1-soap php7.1-mysql php7.1-sockets php7.2 php7.2-bcmath php7.2-cli php7.2-common php7.2-curl php7.2-fpm php7.2-gd php7.2-intl php7.2-json php7.2-mbstring php7.2-mysql php7.2-opcache php7.2-readline php7.2-soap php7.2-xml php7.2-xsl php7.2-zip && \
# COMPOSER #################################################################
curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer && \
#composer global require "hirak/prestissimo:^0.3" && \
# PHP MOD(s) ###############################################################
#ln -s ${PHP_MODS_DIR}/custom.ini ${PHP_CLI_CONF_DIR}/999-custom.ini && \
#ln -s ${PHP_MODS_DIR}/custom.ini ${PHP_CGI_CONF_DIR}/999-custom.ini && \
# CLEAN UP #################################################################
apt-get clean -y && \
apt-get autoclean -y && \
apt-get remove -y wget curl && \
apt-get autoremove -y && \
rm -rf /var/lib/apt/lists/* /var/lib/log/* /tmp/* /var/tmp/*
# FILES (it overrides originals)
#ADD conf.d/custom.ini ${PHP_MODS_DIR}/custom.ini
ADD php.ini /etc/php/7.1/cli/php.ini
ADD /etc/php/7.2/cli/php.ini /etc/php/7.2/cli/php.ini
# WORKDIR
#WORKDIR /srv
# COMMAND
CMD ["php"]
```
