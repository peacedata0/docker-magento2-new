FROM php:7.1-fpm

COPY --from=composer /usr/bin/composer /usr/bin/composer

RUN apt-get update && apt-get install -y \
	git \ 
	netcat \
	mysql-client \
	libfreetype6-dev \
	libicu-dev \
	libjpeg62-turbo-dev \
	libmcrypt-dev \
	libpng-dev \
	libxslt1-dev \
	sendmail-bin \
	sendmail \
	mailutils \
	libbz2-dev \ 
	sudo

RUN docker-php-ext-configure \
	gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

RUN docker-php-ext-configure opcache --enable-opcache

RUN docker-php-ext-install \
	dom \ 
	gd \ 
	intl \ 
	mbstring \ 
	pdo_mysql \ 
	xsl \ 
	zip \ 
	soap \ 
	bcmath \ 
	bz2 \
	opcache \
	mcrypt

RUN pecl install -o -f redis \
	&&  rm -rf /tmp/pear \
	&&  docker-php-ext-enable redis

RUN pecl install -o -f xdebug

ADD conf/php.ini    /usr/local/etc/php/php.ini
ADD conf/mail.ini   /usr/local/etc/php/conf.d/mail.ini
ADD conf/xdebug.ini /usr/local/etc/php/conf.d/xdebug.ini
ADD conf/www.conf /usr/local/etc/php-fpm.d/www.conf
ADD conf/opcache.ini /usr/local/etc/php/conf.d/opcache.ini

ADD entrypoint.sh /entrypoint.sh
RUN ["chmod", "+x", "/entrypoint.sh"]

RUN mkdir -p /magento-code/ \
    && git clone https://github.com/magento/magento2.git /magento-code/ \
    && cd /magento-code/ \ 
    && git checkout 2.3 \
    && chown -R www-data:www-data /magento-code/

ENTRYPOINT ["/entrypoint.sh"]
CMD ["php-fpm", "-F"]