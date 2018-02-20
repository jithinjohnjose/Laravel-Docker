# Laravel + Docker (Nginx, php-fpm 7.1, MySql, Redis)

# Step 1 — grab the latest Laravel release

    a) sudo curl -L https://github.com/laravel/laravel/archive/v5.3.16.tar.gz | tar xz
    b) mv laravel-5.3.16 my-site

# Step 2 — Install dependencies

    a) cd my-site
    b) sudo docker run --rm -v $(pwd):/app composer/composer install

# Step 3 — create the development docker-compose.yml file

    a) Create docker-compose.yml ( Check in files)
  
# Step 4 — Create app.dockerfile, web.dockerfile, nginx configuration and add following
  
    a) create web.dockerfile ( Check content in files added )
  
    b) create app.dockerfile ( Check content in files added )
  
    c) create vhost.conf ( Check configuration in files added )
 
# Step 5 — Build the images and prepare the Laravel Application
   
    a) sudo docker-compose run --build -d

    b) sudo mv .env.example .env
   
    c) Run following commands

      sudo docker-compose exec app php artisan key:generate
      sudo docker-compose exec app php artisan optimize
      sudo docker-compose exec app php artisan config:cache
      sudo docker-compose exec app php artisan config:clear
   
    d) hit http://server-ip:8080 to see LARAVEL page
