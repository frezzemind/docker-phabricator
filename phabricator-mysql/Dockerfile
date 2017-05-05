#
# Docker image for the phabricator database
#

# From docker hub mirror hub.c.163.com
FROM hub.c.163.com/library/debian:jessie
MAINTAINER  freezemind <zhanghan0031@163.com>

ENV DEBIAN_FRONTEND noninteractive
ENV DEBCONF_NONINTERACTIVE_SEEN true

# Set mirrors
RUN echo "deb http://mirrors.163.com/debian jessie main" > /etc/apt/sources.list \
	&& echo "deb http://mirrors.163.com/debian jessie-updates main" >> /etc/apt/sources.list \
	&& echo "deb http://security.debian.org/ jessie/updates main" >> /etc/apt/sources.list \
	&& echo "deb http://mirrors.163.com/debian jessie-backports main" >> /etc/apt/sources.list

# Install dependency
RUN   apt-get update && apt-get install -y \
      curl \
      mysql-server \
      && apt-get clean && rm -rf /var/lib/apt/lists/*

# Set mysql config
RUN     sed -i -e "s/^bind-address\s*=\s*127.0.0.1/bind-address = 0.0.0.0/" \
            /etc/mysql/my.cnf
COPY    phabricator-mysql.cnf /etc/mysql/conf.d/phabricator-mysql.cnf

# Download phabricator quick start sql
COPY    download.sh /opt/mysql/download.sh
RUN     bash /opt/mysql/download.sh

COPY    setup.sh /opt/mysql/setup.sh
RUN     bash /opt/mysql/setup.sh

VOLUME  ["/var/lib/mysql"]
CMD     mysqld_safe