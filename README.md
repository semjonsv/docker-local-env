# Laravel API with Vue SPA frontend 
#### Local PHP Development Environment in Docker
### :fire: Nginx config:
````
server {
    listen 80;
    index index.html index.php;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    
    server_name laravel.local www.laravel.local;
    root /var/www/public;

    location / {
        root /var/dist;
        try_files $uri $uri/ /index.html;
        gzip_static on;
    }

    location /api/ {
        try_files $uri $uri/ /index.php;
    }

    location ~ \.php$ {            
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass laravel:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
````

### 💥 Docker-compose:

````
version: '3'
services:

  web:
    image: nginx:alpine
    container_name: web
    ports:
      - "80:80"
    volumes:
      - ../php-api:/var/www
      - ../vue-front/dist:/var/dist
      - ./nginx/:/etc/nginx/conf.d/
    networks:
      - development

  db:
    image: mysql
    container_name: db
    ports:
      - "3306:3306"
    environment:
      MYSQL_ROOT_PASSWORD: password
    volumes:
      - ./mysql:/var/lib/mysql
    networks:
      - development

  laravel:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: laravel
    volumes:
      - ../php-api/:/var/www
    networks:
      - development

networks:
  development:
````
