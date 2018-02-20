Laravel + Docker (Nginx, php-fpm 7.1, MySql, Redis)


Step 1 — grab the latest Laravel release

  a) sudo curl -L https://github.com/laravel/laravel/archive/v5.3.16.tar.gz | tar xz
  b) mv laravel-5.3.16 my-site

Step 2 — Install dependencies

  a) cd my-site
  b) sudo docker run --rm -v $(pwd):/app composer/composer install

Step 3 — create the development docker-compose.yml file

  a) sudo vim docker-compose.yml and add following

version: '2'
services:

  # The Application
  app:
    build:
      context: ./
      dockerfile: app.dockerfile
    working_dir: /var/www
    volumes:
      - ./:/var/www
    environment:
      - "DB_PORT=3306"
      - "DB_HOST=database"

  # The Web Server
  web:
    build:
      context: ./
      dockerfile: web.dockerfile
    working_dir: /var/www
    volumes_from:
      - app
    ports:
      - 8080:80

  # The Database
  database:
    image: mysql:5.6
    volumes:
      - dbdata:/var/lib/mysql
    environment:
      - "MYSQL_DATABASE=homestead"
      - "MYSQL_USER=homestead"
      - "MYSQL_PASSWORD=secret"
      - "MYSQL_ROOT_PASSWORD=secret"
    ports:
        - "33061:3306"

volumes:
  dbdata:
  
Step 4 — Create app.dockerfile, web.dockerfile, nginx configuration and add following
  
  a) sudo vim web.dockerfile
  
  FROM nginx:1.10

  ADD vhost.conf /etc/nginx/conf.d/default.conf
  
  b) sudo vim app.dockerfile
  
  FROM php:7.1-fpm

  RUN apt-get update && apt-get install -y libmcrypt-dev \
    mysql-client libmagickwand-dev --no-install-recommends \
    && pecl install imagick \
    && docker-php-ext-enable imagick \
    && docker-php-ext-install mcrypt pdo_mysql
   
   c) sudo vim vhost.conf
   
   server {
    listen 80;
    index index.php index.html;
    root /var/www/public;

    location / {
        try_files $uri /index.php?$args;
    }

    location ~ \.php$ {
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
 
 Step 5 — Build the images and prepare the Laravel Application
   
   a) sudo docker-compose run --build -d

   b) sudo mv .env.example .env
   
   c) sudo docker-compose exec app php artisan key:generate
      sudo docker-compose exec app php artisan optimize
      sudo docker-compose exec app php artisan config:cache
      sudo docker-compose exec app php artisan config:clear
   
   d) hit http://server-ip:8080 to see LARAVEL page
