ARG PHP_VERSION=8
FROM php:${PHP_VERSION}-cli

# Install sqlite and libonig-dev (required for building PHP 7.4)
RUN apt-get update && apt-get install -y libsqlite3-dev libonig-dev
# Install custom version of libxml2
RUN apt-get install -y automake libtool unzip libssl-dev
# Remove current version
RUN apt-get remove -y libxml2
# Download new version, configure and compile
ARG LIBXML_VERSION=2.9.12
RUN curl https://gitlab.gnome.org/GNOME/libxml2/-/archive/v$LIBXML_VERSION/libxml2-v$LIBXML_VERSION.zip -o /tmp/libxml.zip && \
	cd /tmp && \
	unzip libxml.zip && \
	cd libxml2-v$LIBXML_VERSION && \
	./autogen.sh --libdir=/usr/lib/x86_64-linux-gnu && \
	make && \
	make install
# Recompile PHP with the new libxml2 library
RUN docker-php-source extract && \
	cd /usr/src/php && \
	./configure \
		--with-libxml-dir=/usr/lib/x86_64-linux-gnu \
		--enable-mbstring \
		--with-openssl \
		--with-config-file-path=/usr/local/etc/php \
		--with-config-file-scan-dir=/usr/local/etc/php/conf.d && \
	make && make install && \
	docker-php-source delete

#RUN apt-get update

# Check if there's a pinned version of Xdebug for compatibility reasons
ARG XDEBUG_VERSION
RUN pecl install xdebug$(if [ ! ${XDEBUG_VERSION} = '' ]; then echo -${XDEBUG_VERSION} ; fi) && docker-php-ext-enable xdebug

# Required by coveralls
RUN apt-get install git -y
