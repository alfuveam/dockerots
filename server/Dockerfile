FROM ubuntu

USER root

# ************************ Config Server ************************

#Install server libs
RUN apt-get update && \
    apt-get install -y \
    supervisor -y \
    git \
    cmake \
    software-properties-common \
    build-essential \
    liblua5.2-dev \
    libgmp3-dev \
    libmariadbclient-dev \
    libboost-system-dev \
    libboost-iostreams-dev \
    libpugixml-dev \
    libcrypto++-dev 

#Workdir
WORKDIR /

#Extract the server
COPY conf/server.tar.gz /server.tar.gz
RUN tar xvzf /server.tar.gz server
RUN mv server /usr/share/otserver

#Compile the server
WORKDIR /usr/share/otserver
RUN mkdir build && \
    cd build && \
    cmake .. && \
    make

RUN cd /usr/share/otserver/build  && \
    mv tfs ../tfs

# ************************ Config Site ************************

#Don't ask about the geolocation
ENV DEBIAN_FRONTEND noninteractive

#Add new repository
RUN add-apt-repository ppa:ondrej/php -y 
RUN echo deb http://nginx.org/packages/mainline/ubuntu/ `lsb_release --codename --short` nginx >> /etc/apt/sources.list 
#Bug fix - TODO remove it
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys ABF5BD827BD9BF62

#Instal php libs
RUN apt-get update && \
    apt-get install -y \ 
    php7.0 \
    apache2 \
    libapache2-mod-php7.0 \
    php7.0-mcrypt \
    php7.0-mbstring \
    php7.0-mysql \
    phpmyadmin

#Configure apache/phpmyadmin
RUN echo "include /etc/phpmyadmin/apache.conf" | tee -a /etc/apache2/apache2.conf
COPY conf/config-db.php /etc/phpmyadmin/config-db.php
COPY conf/dir.conf /etc/apache2/mods-enabled/dir.conf

#Extract site
WORKDIR /home

COPY conf/Gesior.tar.gz /home/Gesior.tar.gz
RUN tar xvzf /home/Gesior.tar.gz Gesior

RUN rm -rf /var/www/html/*
RUN mv Gesior/* /var/www/html/
COPY conf/login.php /var/www/html/login.php

RUN chmod -R 755 /var/www/html && \
    chmod -R 777 /var/www/html/config && \
    chmod -R 777 /var/www/html/cache && \
    chmod -R 777 /var/www/html/custom_scripts

#Restart apache
RUN service apache2 restart

# ************************ Supervisord ************************


COPY conf/startServer.sh /opt/startServer.sh
RUN chmod +x /opt/startServer.sh

#Configure supervisord in order to don't kill the container
COPY conf/supervisord.conf /etc/supervisord.conf
ENTRYPOINT ["/usr/bin/supervisord"]
