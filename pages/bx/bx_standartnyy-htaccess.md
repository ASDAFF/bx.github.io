---
title: Стандартный .htaccess
keywords: sample homepage
sidebar: bx_sidebar
folder: bx
permalink: bx_standartnyy-htaccess.html
summary: false
toc: false
---

## Стандартный .htaccess

Для единообразия формирования URL страниц сайтов, и предотвращения появлений дубликатов страниц, вводится стандартная часть файла .htaccess.

#### Данный конфиг позволяет решить следующие задачи:

* Активация канонических директив
* Активация рекомендованных директив "Битрикс монитор качества"
* Установить основное зеркало сайта с www  сохраняя протокол  http или https
* Установка основного зеркала сайта без www сохраняя http или https
* Перенаправление HTTP > HTTPS
* Перенаправление HTTPS > HTTP
* Удалить любое количество "/" стоящих рядом; site.ru////catalog//item  > site.ru/catalog/item
* Удалять "/" в конце URL если это файл
* Добавлять "/" в конце URL если его там нет и это не файл. (работает в связке с вышестоящим, иногда требуется одно, иногда другое)
* Удалить из URL index.php
* Компрессия статического контента для GooglePagespeed тест
* Добавлен AddType svg

#### Последовательность установки:

1. Вставить код в начале .htaccess
2. При вставке требуется указать правильное зеркало сайта, раскоментировав нужное, по умолчанию удаляет WWW, и включает HTTPS
3. далить старый redirect перенаправление на основное зеркало.
4. Если основное зеркало сайт HTTPS, то внесите протокол в robots.txt Host: https://site.ru, для http не требуется.
5. Убедитесь что SSL сертификат выпущен и для зеркала www, в противном случае редирект не сработает
6. При установке HTTPS основным зеркалом, перейти на свой сайт и убедиться в отсутствие blocked:mixed

#### Полезные сервисы


[Проверить код ответа](https://bertal.ru/index.php?a566754)

[Генератор редиректов](https://donatstudios.com/RewriteRule_Generator)

[Массовая проверка кодов ответа](http://4seo.biz/tools/31/)

[Снипеты](https://klondike-studio.ru/blog/snipet-for-htaccess/)


### Код конфигурационного файла каталога .htaccess.

```apacheconf
############################################################################
#### Стандартный .htaccess для проектов студии Клондайк, версия 4.6     ####
############################################################################
RewriteEngine On
   #  Директива включает редиректы.
RewriteBase / 
   # Без директивы (.*) = /$1 будет /var/wwww/site/web/$1  с директивой  = /$1
Options +FollowSymLinks
   # Разрешает переход по символическим ссылкам.
php_flag display_errors off
  # запретить отображение ошибок  (требование монитора качества)
php_flag allow_url_fopen off
  # запретить  использовать удаленные файлы (требование проактивной защиты)

############################################################################
#### Выбор основного зеркала (с www или без www)                        ####
############################################################################
    # 1. Удалить www
RewriteCond %{ENV:HTTPS} on
    #Если включен https
RewriteRule .* - [E=SSL:s]
    #То создаем переменную  ssl с текстом s
RewriteCond %{HTTP_HOST} ^www\.(.*) [NC]
    # Проверяем, содержит ли домен www в начале URL.
RewriteRule ^(.*)$ http%{ENV:SSL}://%1/$1 [R=301,L]
    # Перенаправляем удаляем www

    # 2. Добавить www
#RewriteCond %{ENV:HTTPS} on
    #Если включен https
#RewriteRule .* - [E=SSL:s]
    #То создаем переменную  ssl с текстом s
#RewriteCond %{HTTP_HOST} !^www\.(.*) [NC]
    # Если нет www в начале домена
#RewriteRule ^(.*)$ http%{ENV:SSL}://www.%{HTTP_HOST}/$1 [R=301,L]
    #Подставляем www и https если он включен.

############################################################################
#### Перенаправляем протокол https на http                              ####
############################################################################
#RewriteCond %{HTTPS} on
   # Проверяем наличие https в URL.
#RewriteRule ^.*$ http://%{SERVER_NAME}%{REQUEST_URI} [R=301,L]
   # Перенаправляем протокол на http.

############################################################################
#### Перенаправляем протокол http на https                              ####
############################################################################
RewriteCond %{HTTPS} off
   # Проверяем наличие https в URL.
RewriteCond %{REQUEST_URI} !^/bitrix/admin/1c_exchange\.php$ [NC] 
   #  Исключим обмен с 1С, ему требуется только 200 
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
   # Перенаправляем протокол на http.

############################################################################
#### Убираем index.php, если он есть в конце URL                        ####
############################################################################
RewriteCond %{REQUEST_URI} ^(.*)/index\.php$
   # URL cодержит index.php в конце.
RewriteCond %{REQUEST_METHOD} =GET
   # Выявляем GET запрос в URL (не POST).
RewriteRule ^(.*)$ %1/ [R=301,L]
   # Удалить index.php из URL.

############################################################################
#### Убираем повторяющиеся слеши (/) в URL                              ####
############################################################################
RewriteCond %{THE_REQUEST} //
   # Проверяем, повторяется ли слеш (//) более двух раз.
RewriteCond %{QUERY_STRING} !http(s|)://
  # Убедимся что это не урл в  GET
RewriteRule .* /$0 [R=301,L]
   # Исключаем все лишние слеши.

############################################################################
#### Убираем слеши в конце URL для статических файлов (содержит точку)  ####
############################################################################
RewriteCond %{REQUEST_URI} \..+$
   # Если файл содержит точку.
RewriteCond %{REQUEST_FILENAME} !-d
   # И это не директория.
RewriteCond %{REQUEST_FILENAME} -f
   # Является файлом.
RewriteCond %{REQUEST_URI} ^(.+)/$
   # И в конце URL есть слеш.
RewriteRule ^(.+)/$ /$1 [R=301,L]
   # Исключить слеш.

############################################################################
#### Добавляем слеш(/), если его нет, и это не файл.                    ####
############################################################################
RewriteCond %{REQUEST_URI} !(.*)/$
   # Если слеша в конце нет.
RewriteCond %{REQUEST_FILENAME} !-f
   # Не является файлом.
RewriteCond %{REQUEST_URI} !\..+$
   # В URL нет точки (файл).
RewriteCond %{REQUEST_URI} ^(.+)$
 # В URL есть хоть один символы
RewriteRule ^(.*)$ $1/ [L,R=301]
   # Добавляем слеш в конце.


############################################################################
#### Компрессия статического контента для гугл  спид тест               ####
############################################################################
<IfModule mod_deflate.c>
  AddType image/svg+xml .svg
  AddOutputFilterByType DEFLATE image/svg+xml  
  AddOutputFilterByType DEFLATE application/rss+xml
  AddOutputFilterByType DEFLATE application/vnd.ms-fontobject
  AddOutputFilterByType DEFLATE application/x-font
  AddOutputFilterByType DEFLATE application/x-font-opentype
  AddOutputFilterByType DEFLATE application/x-font-otf
  AddOutputFilterByType DEFLATE application/x-font-truetype
  AddOutputFilterByType DEFLATE application/x-font-ttf
  AddOutputFilterByType DEFLATE application/x-javascript
  AddOutputFilterByType DEFLATE application/xhtml+xml
  AddOutputFilterByType DEFLATE application/xml
  AddOutputFilterByType DEFLATE font/opentype
  AddOutputFilterByType DEFLATE font/otf
  AddOutputFilterByType DEFLATE font/ttf
  AddOutputFilterByType DEFLATE image/svg+xml
  AddOutputFilterByType DEFLATE image/x-icon
  AddOutputFilterByType DEFLATE text/css
  AddOutputFilterByType DEFLATE text/html
  AddOutputFilterByType DEFLATE text/javascript
  AddOutputFilterByType DEFLATE text/plain
  AddOutputFilterByType DEFLATE text/xml
  AddOutputFilterByType DEFLATE image/svg+xml
</IfModule>
<IfModule mod_expires.c>
  ExpiresActive on
  ExpiresByType image/jpeg "access plus 1 year"
  ExpiresByType image/svg "access plus 1 year"
  ExpiresByType image/gif "access plus 1 year"
  ExpiresByType image/png "access plus 1 year"
  ExpiresByType text/javascript "access plus 1 year"
  ExpiresByType text/css "access plus 1 year"
  ExpiresByType application/javascript "access plus 1 year"
  ExpiresByType application/vnd.ms-fontobject "access plus 1 year"
  ExpiresByType application/x-font-ttf "access plus 1 year"
  ExpiresByType application/x-font-opentype "access plus 1 year"
  ExpiresByType application/x-font-woff "access plus 1 year"
  ExpiresByType image/svg+xml "access plus 1 year"
</IfModule>
  <IfModule mod_headers.c>
  <filesmatch "\.(ico|flv|jpg|jpeg|webp|png|gif|css|swf|woff|pdf)$">
    Header set Cache-Control "max-age=31536000, public"
  </filesmatch>
  <filesmatch "\.(html|htm)$">
    Header set Cache-Control "max-age=7200, private, must-revalidate"
  </filesmatch>
  <filesmatch "\.(pdf)$">
    Header set Cache-Control "max-age=86400, public"
  </filesmatch>
  <filesmatch "\.(js|otf|ttf|woff|woff2)$">
    Header set Cache-Control "max-age=31536000, private"
  </filesmatch>
  </IfModule>
############################################################################
#### Конец общей части, далее следует собственные директивы .htaccess   ####
############################################################################
```

#### Если есть проблема с зацикливанием https

В случае работы nginx+apache возможен циклический  редирект HTTP>HTTPS вызваны неправильными настройками сервера (не файла), Используя на backand http вместо https и по какой-то причине не могут передать протокол обращения от nginx в apache. В таком случае нужно  отключить редирект на https и исправить ошибку или в веб сервере или подобрать подходящее  условие, как правило подойдет:

```apacheconf
    RewriteCond %{HTTP:X-Forwarded-Proto} !https
    RewriteCond %{HTTPS} !on
    RewriteRule (.*) <a href="https://%{HTTP_HOST}%{REQUEST_URI}">https://%{HTTP_HOST}%{REQUEST_URI}</a> [L,R=301]    
```
Или

```apacheconf
    RewriteCond %{HTTPS} !on
    RewriteCond %{SERVER_PORT} ^80$ 
    RewriteCond %{HTTP:CF-Visitor} '"scheme":"http"'
    RewriteRule ^(.*)$ <a href="https://%{HTTP_HOST}%{REQUEST_URI}">https://%{HTTP_HOST}%{REQUEST_URI}</a> [L,R=301]
```

    Если же ни один из вариантов не подошел, просто укажите явно домен вместо %{HTTP_HOST}!
    Не забывайте если {HTTP_HOST} в RewriteCond экранировать спец символы site\.ru

```apacheconf
    RewriteCond %{HTTPS} off
    # Проверяем наличие https в URL.
    RewriteRule ^(.*)$ https://site.ru/$1 [L,R=301]
```

Ровно по той же причине могут возникнуть проблемы с текущим редиректом WWW, в таком случае вам нужно поставить классический редирект, с явным указанием протокола.

### Стандартный редирект для сайта на чистом html

Редиректы для сайтов переведенных на чистый html

```apacheconf
############################################################################
#### Стандартный .htaccess для HTML сайтов                      0.1     ####
############################################################################
  # 1. Удалить www
RewriteCond %{ENV:HTTPS} on
  #Если включен https
RewriteRule .* - [E=SSL:s]
  #То создаем переменную  ssl с текстом s
RewriteCond %{HTTP_HOST} ^www\.(.*) [NC]
  # Проверяем, содержит ли домен www в начале URL.
RewriteRule ^(.*)$ http%{ENV:SSL}://%1/$1 [R=301,L]
  # Перенаправляем удаляем www

  # 2. Добавить www
#RewriteCond %{ENV:HTTPS} on
  #Если включен https
#RewriteRule .* - [E=SSL:s]
  #То создаем переменную  ssl с текстом s
#RewriteCond %{HTTP_HOST} !^www\.(.*) [NC]
  # Если нет www в начале домена
#RewriteRule ^(.*)$ http%{ENV:SSL}://www.%{HTTP_HOST}/$1 [R=301,L]
  #Подставляем www и https если он включен.

############################################################################
#### Перенаправляем протокол https на http                              ####
############################################################################
#RewriteCond %{HTTPS} on
  # Проверяем наличие https в URL.
#RewriteRule ^.*$ http://%{SERVER_NAME}%{REQUEST_URI} [R=301,L]
  # Перенаправляем протокол на http.

############################################################################
#### Перенаправляем протокол http на https                              ####
############################################################################
RewriteCond %{HTTPS} off
  # Проверяем наличие https в URL.
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
  # Перенаправляем протокол на http.


############################################################################
#### Удаляем index.html из URL                                         ####
############################################################################
RewriteRule ^(.*)index\.html$ https://%{HTTP_HOST}/$1 [R=301,L]
RewriteRule ^(.*)index\.htm$ https://%{HTTP_HOST}/$1 [R=301,L]

############################################################################
#### Компрессия статического контента для гугл  спид тест               ####
############################################################################
<IfModule mod_deflate.c>
  AddType image/svg+xml .svg
  AddOutputFilterByType DEFLATE image/svg+xml  
  AddOutputFilterByType DEFLATE application/rss+xml
  AddOutputFilterByType DEFLATE application/vnd.ms-fontobject
  AddOutputFilterByType DEFLATE application/x-font
  AddOutputFilterByType DEFLATE application/x-font-opentype
  AddOutputFilterByType DEFLATE application/x-font-otf
  AddOutputFilterByType DEFLATE application/x-font-truetype
  AddOutputFilterByType DEFLATE application/x-font-ttf
  AddOutputFilterByType DEFLATE application/x-javascript
  AddOutputFilterByType DEFLATE application/xhtml+xml
  AddOutputFilterByType DEFLATE application/xml
  AddOutputFilterByType DEFLATE font/opentype
  AddOutputFilterByType DEFLATE font/otf
  AddOutputFilterByType DEFLATE font/ttf
  AddOutputFilterByType DEFLATE image/svg+xml
  AddOutputFilterByType DEFLATE image/x-icon
  AddOutputFilterByType DEFLATE text/css
  AddOutputFilterByType DEFLATE text/html
  AddOutputFilterByType DEFLATE text/javascript
  AddOutputFilterByType DEFLATE text/plain
  AddOutputFilterByType DEFLATE text/xml
  AddOutputFilterByType DEFLATE image/svg+xml
</IfModule>
<IfModule mod_expires.c>
  ExpiresActive on
  ExpiresByType image/jpeg "access plus 1 year"
  ExpiresByType image/svg "access plus 1 year"
  ExpiresByType image/gif "access plus 1 year"
  ExpiresByType image/png "access plus 1 year"
  ExpiresByType text/javascript "access plus 1 year"
  ExpiresByType text/css "access plus 1 year"
  ExpiresByType application/javascript "access plus 1 year"
  ExpiresByType application/vnd.ms-fontobject "access plus 1 year"
  ExpiresByType application/x-font-ttf "access plus 1 year"
  ExpiresByType application/x-font-opentype "access plus 1 year"
  ExpiresByType application/x-font-woff "access plus 1 year"
  ExpiresByType image/svg+xml "access plus 1 year"
</IfModule>
<IfModule mod_headers.c>
  <filesmatch "\.(ico|flv|jpg|jpeg|webp|png|gif|css|swf|woff|pdf)$">
    Header set Cache-Control "max-age=31536000, public"
  </filesmatch>
  <filesmatch "\.(html|htm)$">
    Header set Cache-Control "max-age=7200, private, must-revalidate"
  </filesmatch>
  <filesmatch "\.(pdf)$">
    Header set Cache-Control "max-age=86400, public"
  </filesmatch>
  <filesmatch "\.(js|otf|ttf|woff|woff2)$">
    Header set Cache-Control "max-age=31536000, private"
  </filesmatch>
</IfModule>
############################################################################
#### Конец общей части, далее следует собственные директивы .htaccess   ####
############################################################################
```