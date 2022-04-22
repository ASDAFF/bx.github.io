---
title: Ошибки и способы их устранения "1С Битрикс"
keywords: cms 1с битрикс, статьи и примеры кода, обучающие материалы
sidebar: articles_sidebar
folder: articles
permalink: articles-bugs-bitrix.html
summary: Статьи и примеры ошибок и способов их устранения на 1С Битрикс
toc: false
---

## Ошибка Mysql query error: (1146) Table 'ХХХХХ.b_sale_trading_platform' doesn't exist (400)
При обновлении 1С-Битрикс до последней версии, иногда остаются лишние файлы, которые мешают работе системы.

Вот код от Техподдержки, который позволяет удалить лишние файлы и папки из системы.

Выполнить можно в командной PHP строке сайта:
`<Ваш сайт>/bitrix/admin/php_command_line.php?lang=ru`

```php
<?php

$modulesfiles = array(
   array(
      'main',
      array(
         '/bitrix/components/bitrix/main.post.list/templates/.default/activity.php',
         '/bitrix/components/bitrix/main.post.list/templates/.default/html_parser.php',
         '/bitrix/components/bitrix/main.post.list/templates/.default/component_epilog.php',
         '/bitrix/components/bitrix/main.ui.grid/templates/.default/js/updater.js',
         '/bitrix/components/bitrix/main.ui.grid/templates/.default/js/updater.min.js',
         '/bitrix/components/bitrix/main.ui.grid/templates/.default/js/updater.map.js',
         '/bitrix/modules/main/install/components/bitrix/main.post.list/templates/.default/activity.php',
         '/bitrix/modules/main/install/components/bitrix/main.post.list/templates/.default/html_parser.php',
         '/bitrix/modules/main/install/components/bitrix/main.post.list/templates/.default/component_epilog.php',
         '/bitrix/modules/main/install/components/bitrix/main.ui.grid/templates/.default/js/updater.js',
         '/bitrix/modules/main/install/components/bitrix/main.ui.grid/templates/.default/js/updater.min.js',
         '/bitrix/modules/main/install/components/bitrix/main.ui.grid/templates/.default/js/updater.map.js',
         '/bitrix/modules/main/install/components/bitrix/ui.tour'
      )
   ),
   array(
      'highloadblock',
      array(
         '/bitrix/modules/highloadblock/lib/highloadblocklang.php',
         '/bitrix/modules/highloadblock/lib/highloadblockrights.php'
      )
   ),
   array(
      'iblock',
      array(
         '/bitrix/modules/iblock/install/components/bitrix/catalog.set.constructor',
         '/bitrix/modules/iblock/install/components/bitrix/catalog/templates/.default/bitrix/catalog.search',
         '/bitrix/components/bitrix/catalog/templates/.default/bitrix/catalog.element',
         '/bitrix/components/bitrix/catalog/templates/.default/bitrix/catalog.search',
         '/bitrix/components/bitrix/catalog/templates/.default/bitrix/catalog.section',
         '/bitrix/components/bitrix/catalog/templates/.default/bitrix/catalog.top',
         '/bitrix/modules/iblock/lib/element.php',
         '/bitrix/modules/iblock/lib/section.php',
         '/bitrix/modules/iblock/lib/sectionelement.php',
         '/bitrix/modules/iblock/lib/sectionproperty.php'
      )
   ),
   array(
      'sale',
      array(
         '/bitrix/modules/sale/lib/tradingplatform.php'
      )
   ),
   array(
      'tasks',
      array(
         '/bitrix/modules/tasks/lib/elapsedtime.php',
         '/bitrix/modules/tasks/lib/member.php',
         '/bitrix/modules/tasks/lib/task.php',
         '/bitrix/modules/tasks/lib/tag.php',
         '/bitrix/modules/tasks/lib/template.php',
         '/bitrix/modules/tasks/lib/task/favorite.php'
      )
   ),
   array(
      'crm',
      array(
         '/bitrix/modules/crm/lib/status.php',
         '/bitrix/modules/crm/lib/invoiceuts.php'
      )
   ),
   array(
      'bizproc',
      array(
         '/bitrix/modules/bizproc/lib/workflowinstance.php',
         '/bitrix/modules/bizproc/lib/workflowstate.php',
         '/bitrix/modules/bizproc/lib/workflowtemplate.php'
      )
   ),
   array(
      'im',
      array(
         '/bitrix/modules/im/lib/botchat.php',
         '/bitrix/modules/im/lib/bottoken.php',
         '/bitrix/modules/im/lib/commandlang.php',
         '/bitrix/modules/im/lib/message.php',
         '/bitrix/modules/im/lib/messageparam.php',
         '/bitrix/modules/im/lib/relation.php',
         '/bitrix/modules/im/lib/status.php'
      )
   ),
   array(
      'mobile',
      array(
         '/bitrix/components/bitrix/mobile.crm.contact.list/templates/.default/script.js',
         '/bitrix/components/bitrix/mobile.crm.company.list/templates/.default/script.js'
      )
   ),
   array(
      'lists',
      array(
         '/bitrix/components/bitrix/lists.export.excel/lang/ru/.description.php',
         '/bitrix/components/bitrix/lists.export.excel/lang/ru/.parameters.php',
         '/bitrix/modules/lists/install/components/bitrix/lists.export.excel/lang/ru/.description.php',
         '/bitrix/modules/lists/install/components/bitrix/lists.export.excel/lang/ru/.parameters.php'
      )
   ),
   array(
      'ui',
      array(
         '/bitrix/components/bitrix/ui.sidepanel.wrapper/templates/.default/style.css',
         '/bitrix/modules/ui/install/components/bitrix/ui.sidepanel.wrapper/templates/.default/style.css'
      )
   )
);
foreach ($modulesfiles as $modules) {
   foreach ($modules as $module) {
      if (is_string($module)){
         echo '<b>'.$module.'</b></br>';
      } else {
         foreach ($module as $path) {
            if (\Bitrix\Main\IO\File::isFileExists($_SERVER['DOCUMENT_ROOT'].$path)) {
               if (\Bitrix\Main\IO\File::deleteFile($_SERVER['DOCUMENT_ROOT'].$path)) {
                  echo '<font color="red"> file delete <b>'.$path.'</b></font></br>';
               };
            } else {
               if (\Bitrix\Main\IO\Directory::isDirectoryExists($_SERVER['DOCUMENT_ROOT'].$path)) {
                  $isdeldir=false;
                  try {
                     \Bitrix\Main\IO\Directory::deleteDirectory($_SERVER['DOCUMENT_ROOT'].$path);
                     $isdeldir=true;
                  } catch (\Bitrix\Main\IO\FileDeleteException $exception) {
                     echo '<font color="red"><b>'.$exception->getMessage().'</b></font></br>';
                  } finally {
                     if ($isdeldir){
                        echo '<font color="red"> directory delete <b>'.$path.'</b></font></br>';
                     }
                  }
               } else {
                  echo '<font color="green"> no file or directory along the path <b>'.$path.'</b></font></br>';
               }
            }
         }
      }
   }
}
?>
```