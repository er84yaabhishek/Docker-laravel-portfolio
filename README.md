
# 🚀 Laravel + Docker Setup Guide

/var/www/project/
│
├── docker-compose.yml
├── Dockerfile
│
└── src/   (Laravel project)

## 🟢 STEP 1 — Project Folder Banao
sudo mkdir -p /var/www/project
cd /var/www/project

##  🟢 STEP 2 — Laravel Project Clone karo
git clone https://github.com/soft84ya/Laravel-portfolio.git src

🟢 STEP 3 — Dockerfile Create karo
nano Dockerfile

FROM php:8.2-apache

RUN apt-get update && apt-get install -y \
    git curl zip unzip \
    libzip-dev \
    nodejs npm \
    && docker-php-ext-install pdo pdo_mysql zip

RUN a2enmod rewrite

ENV APACHE_DOCUMENT_ROOT=/var/www/html/public

RUN sed -ri -e 's!/var/www/html!${APACHE_DOCUMENT_ROOT}!g' \
    /etc/apache2/sites-available/*.conf \
    /etc/apache2/apache2.conf \
    /etc/apache2/conf-available/*.conf

COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

WORKDIR /var/www/html

EXPOSE 80

🟢 STEP 4 — docker-compose.yml Create karo
nano docker-compose.yml

version: '3.8'

services:

  app:
    build: .
    container_name: laravel_app
    volumes:
      - ./src:/var/www/html
    ports:
      - "80:80"
    depends_on:
      - mysql

  mysql:
    image: mysql:8
    container_name: laravel_mysql
    restart: always
    environment:
      MYSQL_DATABASE: laravel_db
      MYSQL_USER: laravel_user
      MYSQL_PASSWORD: secret
      MYSQL_ROOT_PASSWORD: root
    volumes:
      - mysql_data:/var/lib/mysql

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    container_name: laravel_phpmyadmin
    restart: always
    ports:
      - "8080:80"
    environment:
      PMA_HOST: mysql
    depends_on:
      - mysql

volumes:
  mysql_data:

  🟢 STEP 5 — Build & Start Containers
docker compose up -d --build

Check running:

docker ps




Tumhe 3 container dikhne chahiye:

laravel_app

laravel_mysql

laravel_phpmyadmin

🟢 STEP 6 — Laravel Setup (Container ke through)

Ab sab commands container ke through run hongi, local system pe nahi.

🔹 Composer Install
docker compose exec app composer install
🔹 NPM Install
docker compose exec app npm install
🔹 .env file
docker compose exec app cp .env.example .env

🟢 STEP 7 — Database Settings Change karo

Open .env:

docker compose exec app nano .env

Update DB section:

DB_CONNECTION=mysql
DB_HOST=mysql
DB_PORT=3306
DB_DATABASE=laravel_db
DB_USERNAME=laravel_user
DB_PASSWORD=secret

🟢 STEP 8 — App Key Generate
docker compose exec app php artisan key:generate

🟢 STEP 9 — Migrate & Seed
docker compose exec app php artisan migrate
docker compose exec app php artisan db:seed

🟢 STEP 10 — Storage Permission Fix
docker compose exec app chmod -R 777 storage bootstrap/cache

🚀 Access Project
Website
http://YOUR_SERVER_IP
phpMyAdmin
http://YOUR_SERVER_IP:8080

Login:

Server: mysql

Username: laravel_user

Password: secret






🎯 Demo Account
Admin URL:
http://YOUR_SERVER_IP/admin

Email:
test@example.com

Password:
password













