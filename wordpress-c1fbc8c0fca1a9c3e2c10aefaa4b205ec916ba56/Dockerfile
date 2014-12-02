FROM php:5.6-apache
# Install Haproxy.
RUN \
  sed -i 's/^# \(.*-backports\s\)/\1/g' /etc/apt/sources.list && \
  apt-get update && \
  apt-get install -y haproxy && \
  sed -i 's/^ENABLED=.*/ENABLED=1/' /etc/default/haproxy && \
  rm -rf /var/lib/apt/lists/*

# Add files.
ADD haproxy.cfg /etc/haproxy/haproxy.cfg
ADD start.bash /haproxy-start
# Define mountable directories.
VOLUME ["/haproxy-override"]

# Define working directory.
WORKDIR /etc/haproxy
RUN bash -c 'echo "service haproxy start" >> /.bashrc'
# Define default command.
CMD ["bash", "/haproxy-start"]

# Expose ports.

WORKDIR /var/www/html

RUN apt-get update && apt-get install -y rsync && rm -r /var/lib/apt/lists/*

RUN a2enmod rewrite

# install the PHP extensions we need
RUN apt-get update && apt-get install -y libpng12-dev && rm -rf /var/lib/apt/lists/* \
	&& docker-php-ext-install gd \
	&& apt-get purge --auto-remove -y libpng12-dev
RUN docker-php-ext-install mysqli

VOLUME /var/www/html

ENV WORDPRESS_VERSION 4.0.0
ENV WORDPRESS_UPSTREAM_VERSION 4.0
ENV MYSQL_PORT_3306_TCP tcp://127.0.0.1:10000
ENV MYSQL_PORT_3306_TCP_PROTO tcp
ENV MYSQL_PORT_3306_TCP_ADDR 127.0.0.1
ENV MYSQL_ENV_MYSQL_ROOT_PASSWORD password

# upstream tarballs include ./wordpress/ so this gives us /usr/src/wordpress
RUN curl -SL http://wordpress.org/wordpress-${WORDPRESS_UPSTREAM_VERSION}.tar.gz | tar -xzC /usr/src/

COPY docker-entrypoint.sh /entrypoint.sh
# grr, ENTRYPOINT resets CMD now
ENTRYPOINT ["/entrypoint.sh"]
CMD ["apache2", "-DFOREGROUND"]

EXPOSE 80
EXPOSE 443


