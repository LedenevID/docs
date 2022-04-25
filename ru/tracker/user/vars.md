# Переменные

При использовании макросов, триггеров и автодействий вы можете подставить значения из полей задачи в [комментарий](set-action.md#create-comment), [формулу](set-action.md#section_calc_field) или [запрос HTTP](set-action.md#create-http) при помощи переменных. 

## Добавить переменную {#add-variable}

1. Создайте [макрос](../manager/create-macroses.md), [триггер](trigger.md) или [автодействие](autoactions.md).

1. Нажмите **Добавить переменную** в блоке действий триггера или автодействия. Для макроса добавьте переменную в блок сообщения.

1. Выберите поле из списка. При выполнении действия на место переменной в текст будет подставлено значение поля. Вы также можете подставить параметры задачи, которые отсутствуют в списке переменных. Для этого [введите](#variable-type) имя переменной в тексте.
    
    {% note info %}

    В списке отображаются переменные для всех полей, доступных в {{ tracker-name }}. Если вы выберете переменную для поля, которое не используется в очереди или в задаче, значение подставлено не будет.

    {% endnote %}

## Типы переменных {#variable-type}

Имена переменных, которые соответствуют полям задачи, записываются в формате: `{{issue.<ключ поля>}}`. Чтобы получить определенный параметр поля задачи, используйте формат записи: `{{issue.<ключ поля>.<параметр>}}`.

### Атрибуты пользователей {#users-data}

С помощью переменных по умолчанию вы можете получить имя и фамилию пользователя. Чтобы получить определенные атрибуты пользователя, укажите переменную вида `{{issue.<роль>.<атрибут>}}`, где `<роль>` — роль и название поля, в котором указан пользователь; `<атрибут>` — обозначение атрибута.

Роли пользователей:
* `assignee` — исполнитель;
* `author` — автор;
* `followers` — наблюдатели;
* `access` — пользователи в поле **Доступ**.

Атрибуты пользователей:
* `login` — логин; 
* `firstName` — имя;
* `lastName` — фамилия;
* `uid` — идентификатор;
* `email` — адрес электронной почты.

Например, при помощи переменной `not_var{{issue.followers.email}}` можно получить адреса электронной почты всех наблюдателей задачи.

### Дополнительные параметры задачи {#extra-data}

Некоторые параметры задачи не отображаются в полях, но их значения также можно получить с помощью переменных:

Переменная | Значение 
----- | -----
`not_var{{issue.parent}}` | Родительская задача
`not_var{{issue.previousStatus}}` | Предыдущий статус задачи
`not_var{{issue.previousQueue}}` | Предыдущая очередь задачи
`not_var{{issue.checklistDone}}` | Количество выполненных пунктов чеклиста
`not_var{{issue.votes}}` | Количество голосов за задачу

### Локальные поля {#local-fields}

Имена переменных, которые соответствуют локальным полям задачи, записываются в формате: `{{issue.local.<ключ поля>}}`.

### Модификаторы даты и времени {#date-time}

По умолчанию дата и время передается в формате `ДД месяц ГГГГ`, например: `07 декабря 2021`. Для записи даты и времени в других форматах используйте модификаторы:
* `iso8601` — формат ISO 8601;
* `unixEpoch` — формат Unix Time;
* `date` — запись только даты для полей, в которых передается дата и время.

Примеры записи переменных с модификаторами даты и времени:

Переменная | Значение | Формат записи
----- | ----- | -----
`not_var{{currentDateTime.iso8601}}` | Текущая дата и время в формате ISO 8601 | `YYYY-MM-DDThh:mm:ss.sssZ`
`not_var{{currentDateTime.unixEpoch}}` | Текущее время в формате Unix Time | `1638735223`
`not_var{{currentDateTime.date}}` | Текущая дата | `06 декабря 2021`
`not_var{{issue.start.iso8601}}` | Дата начала задачи в формате ISO 8601 | `YYYY-MM-DD`
`not_var{{issue.start.unixEpoch}}` | Время начала задачи в формате Unix Time | `1638855321`

### Модификаторы JSON {#json-variable}

В полях может быть записано несколько значений. Для получения или передачи значений таких полей, например, в [запросах HTTP](set-action.md#create-http), их нужно преобразовать в формат JSON. Для этого к имени переменной добавьте `.json`.

Примеры записи переменных в формате JSON:

Переменная | Значение | Формат записи
----- | ----- | -----
`not_var{{issue.summary.json}}` | Название задачи | `"Название задачи"`
`not_var{{issue.description.json}}`| Описание задачи | `"Описание"`
`not_var{{issue.tags.json}}` | Теги | `["tag1","tag2"]`
`{{issue.<роль>.login.json}}` | Логин пользователя (для полей `author` и `assignee`) | `"ivan-ivanov"`
`{{issue.<роль>.uid.json}}` | Идентификатор пользователя (для полей `author` и `assignee`) | `1120000000211495`
`{{issue.<роль>.login.json}}` | Логины пользователей (для полей `followers` и `access`) | `["ivan-ivanov", "user3993"]`
`{{issue.<роль>.uid.json}}` | Идентификаторы пользователей (для полей `followers` и `access`) | `[1120000000211495, 1120000000011060]`

{% note warning %}

{{ tracker-name }} поддерживает передачу значений в формате JSON для простых типов полей, которые имеют формат строк, чисел, а также массивов строк и чисел. Тип поля "Объект" не поддерживается: например, переменная `not_var{{issue.author.json}}` работать не будет. Вместо нее можно использовать переменную для получения простого атрибута поля: `not_var{{issue.author.login.json}}`.

{% endnote %}