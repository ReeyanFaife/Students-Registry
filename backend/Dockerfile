FROM php:8.2-apache
RUN docker-php-ext-install pdo pdo_mysql
COPY . /var/www/html/
RUN sed -ri -e 's!DocumentRoot /var/www/html!DocumentRoot /var/www/html/public!' /etc/apache2/sites-available/*.conf \
 && sed -ri -e 's!<Directory /var/www/html>!<Directory /var/www/html/public>!' /etc/apache2/apache2.conf
EXPOSE 80
