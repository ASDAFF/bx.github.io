---
title: Конфигурация сервера
keywords: sample homepage
sidebar: lr_sidebar
folder: laravel
permalink: lr_server-configuration.html
summary: false
toc: false
---

## Конфигурация сервера

### Nginx

Если вы развертываете свое приложение на сервере, на котором работает Nginx, то вы можете использовать следующий конфигурационный файл в качестве отправной точки для настройки веб-сервера. Скорее всего, этот файл нужно будет настроить в зависимости от конфигурации вашего сервера. Если вам нужна помощь в управлении вашим сервером, рассмотрите возможность использования собственной службы управления и развертывания серверов Laravel, такой как Laravel Forge.

Убедитесь, что, как и в конфигурации ниже, ваш веб-сервер направляет все запросы в файл public/index.php вашего приложения. Вы никогда не должны пытаться переместить файл index.php в корень вашего проекта, поскольку обслуживание приложения из корня проекта откроет доступ ко многим конфиденциальным файлам конфигурации из общедоступной сети Интернет:
```
server {
    listen 80;
    listen [::]:80;
    server_name example.com;
    root /srv/example.com/public;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```