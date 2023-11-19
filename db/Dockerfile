FROM mysql:5.7.25

ENV MYSQL_ROOT_PASSWORD="vpropass"
ENV MYSQL_DATABASE="accounts"

ADD db_backup.sql docker-entrypoint-initdb.d/db_backup.sql

EXPOSE 3306


# FROM mysql:8.0.33
# LABEL author="Abdelrhman"

# ENV MYSQL_ROOT_PASSWORD="pass"
# ENV MYSQL_DATABASE="myDB"
# ADD db_backup.sql /docker-entrypoint-initdb.d/.