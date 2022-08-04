---
title: Зацикливание обработчиков событий
keywords: cms 1с битрикс, статьи и примеры кода, обучающие материалы
sidebar: articles_sidebar
folder: articles
permalink: articles-bugs-events-bitrix.html
summary: Зацикливание обработчиков событий
toc: false
---

## Зацикливание обработчиков событий

Имеем достаточно распространенную задачу - при изменении элемента инфоблока модифицировать другой. Кейс может быть какой угодно - это и логирование, и деактивация основного товара, когда нет активных предложений, и изменение даты активности связаного элемента. Никаких проблем, скажете вы. За 10 минут пишется обработчик, использующий метод CIBlockElement::Update, вешается на событие OnBeforeIBlockElementUpdate / OnAfterIBlockElementUpdate, вызывается тестовый пример, сервер падает... Epic fail в чистом виде...

После непродолжительной (или очень продолжительной) отладки выясняется то, о чем следовало бы подумать сразу - идет рекурсивный вызов обработчика. Я бы не заострялся на этом моменте, но неоднократное появление на сайте идей и форуме пожеланий внедрить возможность отключения механизма событий заставляет "расставить все точки над i".

Ниже приведен код, который избавляет от подобных проблем. В качестве примера взят обработчик OnAfterIBlockElementUpdate.

```php
class myClass 
{ 
   protected static $handlerDisallow = false; 
 
   public static function iblockElementUpdateHandler(&$fields) 
   { 
      /* проверяем, что обработчик уже запущен */
      if (self::$handlerDisallow) 
           return; 
       /* взводим флаг запуска */
      self::$handlerDisallow = true;           
      /*  наш код, приводящий к вызову CIBlockElement::Update */ 
      ... 
      CIBlockElement :: Update (..., ...); 
 
      /* вновь разрешаем запускать обработчик */ 
      self::$handlerDisallow = false;
   } 
} 
```
Поясню. За основу взят класс, т.к. в основном речь о собственных модулях. В классе имеется статическая булева переменная - $handlerDisallow. По умолчанию они имеет значение false - нет запрета. В самом начале обработчика необходимо проверять ее значение. Если обработчик уже запущен, она будет равна true и выполнение необходимо прервать. Если же выполнять обработчик можно, необходимо присвоить этой переменной true на время выполнения все обработчика. В конце необходимо флаг ($handlerDisallow) сбросить, иначе до конца хита Ваш обработчик не выполнится больше ни разу.

Если Вы используете в качестве обработчика обычную функцию, не класс - не беда. Создайте статическую переменную внутри функции.

Update. Дополним класс возможностью блокировать работу обработчика "снаружи". Для этого изменим тип переменной и добавим три метода:

```php
class myClass
{
   protected static $handlerDisallow = 0;

   public static function disableHandler()
   {
      self::$handlerDisallow--;
   }

   public static function enableHandler()
   {
      self::$handlerDisallow++;
   }

   public static function isEnabledHandler()
   {
      return (self::$handlerDisallow >= 0);
   }

   public static function iblockElementUpdateHandler(&$fields)
   {
      /* проверяем, что обработчик уже запущен */
      if (!self::isEnabledHandler())
         return;
      /* взводим флаг запуска */
      self::disableHandler();
      /*  наш код, приводящий к вызову CIBlockElement::Update */
      ...
      CIBlockElement :: Update (..., ...);

      /* вновь разрешаем запускать обработчик */
      self::enableHandler();
   }
}
```