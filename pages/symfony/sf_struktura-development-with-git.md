---
title: Ведение разработки с использованием GIT и Битрикс24
keywords: sample homepage
sidebar: sf_sidebar
folder: symfony
permalink: sf_struktura-development-with-git.html
summary: false
toc: false
---

## Общие принципы работы над проектом и задачами

Все задачи поступают сотруднику в Битрикс24. 
Работа над задачей должна вестись в отдельной локальной ветке репозитория проекта.

Каждый проект должен содержать 2е ветки доступные в общем репозитории:

  *  **master** - стабильная ветка проекта. Боевой сервер всегда отображает данные из этой ветки.
  *  **develop** - ветка разработки. Тестовый сервер большую часть времени отображает данные этой ветки. 
    При необходимости ветки на тестовом сервере могут переключаться на основную ветку (master) или на ветку релиза (release/<ГГГГММДД>)

### Работа над задачей

При работе над задачей исполнитель должен:
  
  1. Переместить задачу над которой ведется работа в столбец "В работе" доски Kanban Б24  
  2. Актуализировать локальную ветку develop из основного репозитория
  3. Создать новую ветку на основе ветки develop. Имя ветки должно строиться по шаблону ```feature/<номер задачи в Битрикс24>```  

### Передача на тестирование

После проведения работ задача должна быть передана на тестирование:

  1. Актуализировать локальную ветку develop из основного репозитория
  2. Влить ветку задачи в develop, решить конфликты при их возникновении
  3. Протолкнуть develop в общий репозиторий проекта
  4. Удалить ветку фичи
  5. Переместить задачу в столбец "Готово" доски Kanban Б24

### Подготовка к релизу

При наступлении даты подготовки к релизу ответственный сотрудник должен:

  1. Определить список фич, которые должны попасть в релиз
  2. Определить коммит в ветке develop от которого должен начаться релиз
  3. Создать ветку релиза от коммита ветки develop, найденного на шаге 2. Имя ветки должно строиться по шаблону ```release/<ГГГГММДД>```, где "<ГГГГММДД>" - дата в указанном формате дня создания ветки.
  4. Переключить тестовый сервер на ветку релиза.
  5. Уведомить сотрудников, ответственных за фичи попавшие в релиз, и сотрудников, ответственных за тестирование релиза, о переключении тестового сервера на ветку релиза и начале проведения работ по его тестированию и стабилизации для выката в бой.

### Тестирование и исправления

Процесс тестирования и доработки фич попавших в релиз:

  1. При начале тестирования задачи ответственный сотрудник должен поместить ее в столбец "Тестирование" kanban доски Б24
  2. В случае обнаружения несоответствий и исправлений по задаче - добавить комментарий к задаче в Б24 с описанием пунктов для доработки и перенести ее в столбец "Исправления".
  3. Разработчик фичи берет задачу из столбца "Исправления" и переносит ее в столбец "В работе". Работы по исправлению проводятся на **ветке релиза**, без создания каких-либо иных веток.
  
### Выкат релиза в бой

  1. После завершения всех исправления и успешного прохождения финального тестирования ветка релиза вливается ответственным сотрудником в ветки **master** и **develop**
  2. Ветка master на боевом сервере обновляется и актуализируется в соответствии с релизом
  3. На ветке **master** коммит релиза помечается тегом равным имени ветки релиза
  4. Ветка релиза удаляется
  
### Внесение исправлений на боевом сервере после публикации релиза

При обнаружении недоработок на боевом сервере: 

  1. Разработчики получают соответствующую задачу в Б24
  2. От ветки **master** создается ветка исправления. Имя ветки должно строиться по шаблону ```hotfix/<Номер задачи в Б24>```
  3. После внесения исправлений ветка исправления вливается в ветки **master** и **develop**

Тестирование выполнения задачи исправления проводится на боевом сервере.

## Пример команд GIT при работе с задачами

### Начало работы над задачей

### Отправка задачи на тестирование

### Перенос задачи на боевой сервер