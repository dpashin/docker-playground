FROM ubuntu:14.04

MAINTAINER dpashin <dmitriy.pashin@yandex.ru>

# prevent installing some stuff into / instead of /root
# see https://github.com/dotcloud/docker/issues/2968
ENV HOME /root

ENV DEBIAN_FRONTEND noninteractive

## Install dependencies
RUN apt-get update && apt-get install -y git git-core curl wget build-essential libxml2-dev libssl-dev libcurl4-gnutls-dev libjpeg-dev libpng12-dev libmcrypt-dev libreadline-dev libtidy-dev libxslt1-dev autoconf re2c bison

# no need, can remove safely.
RUN sudo apt-get -y install mc

# install phpenv
RUN git clone git://github.com/CHH/phpenv.git /tmp/phpenv
ENV PHPENV_ROOT /usr/local/phpenv
RUN /tmp/phpenv/bin/phpenv-install.sh

# Add phpenv to the path:
RUN echo '# phpenv setup' > /root/.bashrc
RUN echo 'export RBENV_ROOT=/usr/local/phpenv' >> /root/.bashrc
RUN echo 'export PATH="$RBENV_ROOT/bin:$PATH"' >> /root/.bashrc
RUN echo 'eval "$(rbenv init -)"' >> /root/.bashrc

# Install php-build
RUN git clone git://github.com/CHH/php-build.git /usr/local/php-build
RUN /usr/local/php-build/install.sh

# install php
RUN php-build 5.4.29 /usr/local/phpenv/versions/5.4.29

# register and rehash php
RUN /usr/local/phpenv/bin/phpenv global 5.4.29
RUN /usr/local/phpenv/bin/phpenv rehash

# todo: configure some php-fpm vars
RUN cp /usr/local/phpenv/versions/5.4.29/etc/php-fpm.conf.default /usr/local/phpenv/versions/5.4.29/etc/php-fpm.conf
RUN sed -i 's|^;pid = run/php-fpm.pid$|pid = run/php-fpm.pid|' /usr/local/phpenv/versions/5.4.29/etc/php-fpm.conf
RUN sed -i 's|^user = nobody$|user = www-data|' /usr/local/phpenv/versions/5.4.29/etc/php-fpm.conf

# register php-fpm a service
# TODO replace upstart to init.d, not working :(
RUN echo 'description "PHP FastCGI Process Manager"' > /etc/init/php-fpm.conf
RUN echo 'start on (net-device-up and local-filesystems)' >> /etc/init/php-fpm.conf
RUN echo 'stop on runlevel [016]' >> /etc/init/php-fpm.conf
RUN echo 'expect fork' >> /etc/init/php-fpm.conf
RUN echo 'respawn' >> /etc/init/php-fpm.conf
RUN echo "exec /usr/local/phpenv/versions/5.4.29/sbin/php-fpm --fpm-config /usr/local/phpenv/versions/5.4.29/etc/php-fpm.conf" >> /etc/init/php-fpm.conf

# RUN service php-fpm start
