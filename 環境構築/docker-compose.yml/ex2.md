
```
version: "3.8"   # docker-compose.yml の世代だが、今は不要なので書かなくてOK

services:
  nginx:
    image: nginx:1.21.1
    ports:
      - "80:80"  
    volumes:
      - ./docker/nginx/default.conf:/etc/nginx/conf.d/default.conf  
      - ./src:/var/www/  
    depends_on:   
      - php

  php:
    build: ./docker/php
    volumes:
      - ./src:/var/www/

  mysql:
    image: mysql:8.0.26
    environment:
      MYSQL_ROOT_PASSWORD: root
      MYSQL_DATABASE: laravel_db
      MYSQL_USER: laravel_user
      MYSQL_PASSWORD: laravel_pass
    command: mysqld --default-authentication-plugin=mysql_native_password    #MySQL8.0から認証方式変更の為この引数が必要
    volumes:
      - ./docker/mysql/data:/var/lib/mysql
      - ./docker/mysql/conf.d:/etc/mysql/conf.d:ro

  phpmyadmin:
    image: phpmyadmin/phpmyadmin
    environment:
      - PMA_ARBITRARY=1
      - PMA_HOST=mysql
      - PMA_USER=laravel_user
      - PMA_PASSWORD=laravel_pass
    depends_on:
      - mysql
    ports:
      - 8080:80
```


[[nginxサービスのポイント]]




