#
# Docker image for running https://github.com/phacility/phabricator
#

# From docker hub mirror hub.c.163.com
FROM hub.c.163.com/library/debian:jessie
MAINTAINER  freezemind <zhanghan0031@163.com>

ENV DEBIAN_FRONTEND=noninteractive DEBCONF_NONINTERACTIVE_SEEN=true

# Set mirrors
RUN echo "deb http://mirrors.163.com/debian jessie main" > /etc/apt/sources.list \
	&& echo "deb http://mirrors.163.com/debian jessie-updates main" >> /etc/apt/sources.list \
	&& echo "deb http://security.debian.org/ jessie/updates main" >> /etc/apt/sources.list \
	&& echo "deb http://mirrors.163.com/debian jessie-backports main" >> /etc/apt/sources.list

# Install dependency
# TODO: review this dependency list
RUN apt-get clean && apt-get update && apt-get install -y \
    git \
    apache2 \
    curl \
    libapache2-mod-php5 \
    libmysqlclient18 \
    mercurial \
    mysql-client \
    php-apc \
    php5 \
    php5-apcu \
    php5-cli \
    php5-curl \
    php5-gd \
    php5-json \
    php5-ldap \
    php5-mysql \
    python-pygments \
    sendmail \
    subversion \
    tar \
    sudo \
    && apt-get clean && rm -rf /var/lib/apt/lists/*

# Clone phabricator stable branch
WORKDIR /opt
RUN git clone https://github.com/phacility/phabricator.git phabricator \
    && git clone https://github.com/phacility/arcanist.git arcanist  \
    && git clone https://github.com/phacility/libphutil.git libphutil \

# Setup apache
RUN a2enmod rewrite
COPY    phabricator.conf /etc/apache2/sites-available/phabricator.conf
RUN ln -s /etc/apache2/sites-available/phabricator.conf \
    /etc/apache2/sites-enabled/phabricator.conf \
    && rm -f /etc/apache2/sites-enabled/000-default.conf

# Setup phabricator
RUN mkdir -p /opt/phabricator/conf/local /var/repo
COPY    local.json /opt/phabricator/conf/local/local.json
RUN sed -e 's/post_max_size =.*/post_max_size = 32M/' \
    -e 's/upload_max_filesize =.*/upload_max_filesize = 32M/' \
    -e 's/;opcache.validate_timestamps=.*/opcache.validate_timestamps=0/' \
    -i /etc/php5/apache2/php.ini
RUN ln -s /usr/lib/git-core/git-http-backend /opt/phabricator/support/bin
RUN /opt/phabricator/bin/config set phd.user "root"
RUN echo "www-data ALL=(ALL) SETENV: NOPASSWD: /opt/phabricator/support/bin/git-http-backend" >> /etc/sudoers

EXPOSE  80
COPY    entrypoint.sh /entrypoint.sh
ENTRYPOINT  ["/entrypoint.sh"]
CMD ["start-server"]