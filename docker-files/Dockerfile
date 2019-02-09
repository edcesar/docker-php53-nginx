FROM edcesar/php53-nginx:latest
MAINTAINER Ed Cesar <edcesar@edcesar.com>

ENV OS_LOCALE="pt_BR.UTF-8"
RUN locale-gen ${OS_LOCALE}
ENV LANG=${OS_LOCALE} \
	LC_ALL=${OS_LOCALE} \
	NGINX_CONF_DIR=/etc/nginx

ENV PHP_RUN_DIR=/run/php \
    PHP_LOG_DIR=/var/log/php \
    PHP_CONF_DIR=/etc/php5 \
    PHP_DATA_DIR=/var/lib/php5

COPY ./supervisord.conf /etc/supervisor/conf.d/

RUN echo "apc.enable_cli=1" >> ${PHP_CONF_DIR}/cli/conf.d/apc.ini 

COPY ./configs-php/php-fpm.conf ${PHP_CONF_DIR}/fpm/php-fpm.conf
COPY ./configs-php/www.conf ${PHP_CONF_DIR}/fpm/pool.d/www.conf
COPY ./configs-php/php.ini ${PHP_CONF_DIR}/fpm/conf.d/custom.ini

RUN sed -i "s~PHP_RUN_DIR~${PHP_RUN_DIR}~g" ${PHP_CONF_DIR}/fpm/php-fpm.conf \
    && sed -i "s~PHP_LOG_DIR~${PHP_LOG_DIR}~g" ${PHP_CONF_DIR}/fpm/php-fpm.conf \
    && chown www-data:www-data ${PHP_DATA_DIR} -Rf

COPY ./configs/nginx.conf ${NGINX_CONF_DIR}/nginx.conf
COPY ./configs/app.conf ${NGINX_CONF_DIR}/conf.d/default.conf
COPY ./configs/www.conf /etc/php5/fpm/pool.d/www.conf

RUN chmod 777 /var/www/html

WORKDIR /var/www/html

CMD service nginx start && \
/usr/bin/supervisord && \
/usr/sbin/php5-fpm && \
 tail -f /dev/null
