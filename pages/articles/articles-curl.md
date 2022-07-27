---
title: Установить cURL на VMBitrix / BitrixVM в Битрикс
sidebar: articles_sidebar
keywords: виртуальная машина Битрикс cURL
permalink: articles-curl.html
toc: false
folder: articles
---

## Довольно часто на сайтах 1С-Битрикс требуется установленная библиотека cURL.

* yum install php-curl
* Пакет установлен, но не факт что подключено расширение php
* ls /etc/php.d/
* Скорее всего там будет лежать файл curl.ini.disabled
* mv /etc/php.d/curl.ini.disabled /etc/php.d/curl.ini
* systemctl restart httpd.service
