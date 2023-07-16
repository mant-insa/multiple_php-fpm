# Инструкция по добавлению вашего проекта в текущий конфиг

1. Добавьте в docker-compose.yml контейнер php-fpm с названием проекта в нижнем регистре в качестве имени контейнера, укажите путь к папке вашего проекта в volumes и container_name
```
  project_1:
    build:
      context: .
      dockerfile: _docker/app/Dockerfile
    volumes:
      - ./scripts/project_1/:/var/www/scripts/project_1/
    container_name: project_1
```
2. В docker-compose.yml на строке 10 находится список сервисов, после запуска которых включается nginx. Добавьте в конец списка имя своего сервиса по примеру уже указанных
3. Добавьте сервер в nginx/conf.d/nginx.conf по примеру:
```
server {
    listen {внешний_http_порт};
    listen {внешний_https_порт}; #значения внешних портов nginx можно увидеть в docker-compose.yml
    root /var/www/scripts/{имя_проекта} #например 'project_1'
    include /etc/nginx/conf.d/ssl_config.conf;
    
    location / {
        try_files $uri $uri/ /index.html;
        include /etc/nginx/conf.d/cache.conf;
    }

    location ~ \.php$ {
        include fastcgi_params;
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass {имя_проекта}:9000;
        fastcgi_index {имя_скрипта}.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
    }
}
```
4. Пример локальной ссылки:
```
http://localhost:1024/script_1.php
https://localhost:2024/script_1.php
```