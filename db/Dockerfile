FROM mysql:5.7

#Environment varibales
ENV MYSQL_ROOT_PASSWORD 159Tibia*
ENV MYSQL_DATABASE otserver

#Install mysql libs
RUN apt-get update && \
    apt-get install mysql-server -y

#Db dump
COPY conf/otserver.sql /docker-entrypoint-initdb.d/otserver.sql