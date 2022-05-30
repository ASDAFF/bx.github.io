---
title: git — ошибка file name too long
keywords: статьи о git, советы работы с git
sidebar: articles_sidebar
folder: articles
permalink: articles-g-too-long.html
summary: Статьи и советы по работе с Git
toc: false
---

## git — ошибка file name too long

**Задача:**  При выполнении команды git clone отображается сообщение об ошибке: file name too long

**Решение:** По умолчанию, если использовать git под Windows, есть ограничение на длину пути в 260 символов. Это ограничение касается старого API, которое осталось для обратной совместимости. Более подробно об этом ограничении можно ознакомится в статье [Maximum Path Length Limitation (eng)](https://docs.microsoft.com/en-us/windows/win32/fileio/naming-a-file#maximum-path-length-limitation) .

Грубо говоря, если у Вас есть путь к файлу, который превышает 260 символов — у Вас будет отображаться ошибка file name too long. Для того, чтобы убрать данную ошибку — нужно изменить значение свойства core.longpaths с помощью команды

`git config --system core.longpaths true`

PS: Обратите внимание, в моем случае используется ключ `—system`, который устанавливает настройки для всей системы. Вместо него можно переопределить настройку для всех проектов текущего пользователя (`—global`) или для конкретного проекта (`—local`).
 