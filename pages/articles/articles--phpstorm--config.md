---
title: Статьи PhpStorm | Автоматическая генерация минифицированных js и css файлов в PhpStorm
keywords: статьи о PhpStorm, настройка PhpStorm для работы с 1С Битрикс
sidebar: articles_sidebar
folder: articles
permalink: articles--phpstorm--config.html
summary: Статьи и советы по настройке и работе с PhpStorm
toc: false
---


Если хотите, что бы при верстке сайтов в phpstorm автоматически генерировались *.min.js и *.min.css, само собой, реально сжатые.

Устанавливаем в систему NodeJS
В терминале выполняем команды:

`npm install -g csso-cli`

`npm install -g uglify-js`

В проекте добавляем два watcher-а: CSSO CSS Optimizer и UglifyJS. Как есть, ни чего не трогая в настройках.

И все. просто верстаем в обычных *.js и *.css, минифицированные будут создаваться и обновляться рядом с ними, автоматически.