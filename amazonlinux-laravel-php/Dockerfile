FROM amazonlinux:2 as intermediate

# Install php
RUN amazon-linux-extras install -y php7.4

# Install dependencies and useful tools
RUN yum -y install \
    autoconf \
    automake \
    gcc \
    gcc-c++ \
    git \
    iputils \
    libmemcached-devel \
    libtool \
    libuuid-devel \
    make \
    nc \
    net-tools \
    openssl \
    php \
    php-common \
    php-devel \
    php-fpm \
    php-gd \
    php-mbstring \
    php-mysqlnd \
    php-opcache \
    php-pdo \
    php-pear \
    php-xml \
    pkgconfig \
    procps \
    telnet \
    tree \
    unzip \
    which \
    zlib-devel

# Install libzmq
RUN curl -o /tmp/zeromq-4.0.5.tar.gz https://juwai-devops.s3.cn-north-1.amazonaws.com.cn/zeromq/zeromq_4.0.5/zeromq-4.0.5.tar.gz \
    && cd /tmp/ \
    && tar zxvf zeromq-4.0.5.tar.gz \
    && cd zeromq-4.0.5 \
    && ./configure \
    && make \
    && make install

# Install zmq php extension
RUN cd /tmp/ \
    && git clone https://github.com/mkoppanen/php-zmq.git \
    && cd php-zmq \
    && git checkout fc6e966 \
    && phpize \
    && ./configure \
    && make \
    && make install

# Install msgpack php extension
RUN cd /tmp/ \
    && git clone https://github.com/msgpack/msgpack-php.git \
    && cd msgpack-php \
    && git checkout msgpack-2.0.3 \
    && phpize \
    && ./configure \
    && make \
    && make install

# Install memcached php extension
RUN yes '' | pecl install memcached

# Install composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php composer-setup.php \
    && mv composer.phar /usr/local/bin/composer


FROM amazonlinux:2

# Copy files from intermediate image
COPY --from=intermediate /etc /etc
COPY --from=intermediate /usr/bin /usr/bin
COPY --from=intermediate /usr/bin/php /usr/bin/php
COPY --from=intermediate /usr/lib64 /usr/lib64
COPY --from=intermediate /usr/libexec /usr/libexec
COPY --from=intermediate /usr/local/bin/composer /usr/local/bin/composer
COPY --from=intermediate /usr/local/lib /usr/local/lib
COPY --from=intermediate /usr/sbin /usr/sbin
COPY --from=intermediate /usr/sbin/php-fpm /usr/sbin/php-fpm
COPY --from=intermediate /var/log/php-fpm /var/log/php-fpm

# Copy config files
COPY files/zmq.ini /etc/php.d/
COPY files/msgpack.ini /etc/php.d/
COPY files/www.conf /etc/php-fpm.d/www.conf
COPY files/memcached.ini /etc/php.d/50-memcached.ini

# Disable host key checking for github.com
ADD files/ssh-config /root/.ssh/config

# --nodaemonize forces php-fpm to stay in the foreground regardless of
# daemonize setting in php-fpm.conf, so the package's shipped config can't
# change across PHP versions and silently background the process
CMD ["/usr/sbin/php-fpm", "--fpm-config", "/etc/php-fpm.conf", "--nodaemonize"]
