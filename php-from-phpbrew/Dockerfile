FROM ubuntu:14.04

ENV HOME /root

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update

# install prerequisites for phpbrew
# rtfm https://github.com/phpbrew/phpbrew/blob/7adc9af49be10982348e68699100496a03188707/README.md
RUN apt-get -y install autoconf automake curl build-essential libxslt1-dev re2c libxml2-dev php5-cli
RUN apt-get -y build-dep php5

# get phpbrew
RUN cd /tmp
RUN curl -L -O https://raw.github.com/phpbrew/phpbrew/master/phpbrew
RUN chmod +x phpbrew
RUN mv phpbrew /usr/bin/phpbrew

# init phpbrew
RUN phpbrew init
RUN echo  "\\nsource ~/.phpbrew/bashrc\\n" >> /root/.bashrc

# install custom php
RUN phpbrew install 5.4.29 +fpm +openssl +default +dbs +apxs2 +soap +icu +intl +gd -- --with-pcre-dir="/usr/lib/x86_64-linux-gnu/"
