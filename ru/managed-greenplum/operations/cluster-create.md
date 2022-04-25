# Создание кластера

Кластер {{ mgp-name }} состоит из хостов-мастеров, которые принимают запросы от клиента, и хостов-сегментов, которые обеспечивают обработку и хранение данных.

Подробнее см. в разделе [{#T}](../concepts/index.md).

## Как создать кластер {{ mgp-name }} {#create-cluster}

{% list tabs %}

* Консоль управления

    1. В консоли управления выберите каталог, в котором нужно создать кластер баз данных.
    1. Выберите сервис **{{ mgp-name }}**.
    1. Нажмите кнопку **Создать кластер**.
    1. Введите имя кластера. Оно должно быть уникальным в рамках каталога.
    1. (Опционально) Введите описание кластера.
    1. Выберите окружение, в котором нужно создать кластер (после создания кластера окружение изменить невозможно):
        * `PRODUCTION` — для стабильных версий ваших приложений.
        * `PRESTABLE` — для тестирования, в том числе самого сервиса {{ mgp-full-name }}. В Prestable-окружении раньше появляются новые функциональные возможности, улучшения и исправления ошибок. При этом не все обновления обеспечивают обратную совместимость.
    1. Выберите версию {{ GP }}.
    1. (Опционально) Выберите группы [выделенных хостов](../../compute/concepts/dedicated-host.md), на которых будет размещен кластер.
       
        {% include [Dedicated hosts note](../../_includes/mdb/mgp/note-dedicated-hosts.md) %}

    1. В блоке **Сетевые настройки**:
        * Выберите облачную сеть для размещения кластера.
        * В параметре **Группы безопасности** укажите [группу безопасности](../operations/connect.md#configuring-security-groups), которая содержит правила, разрешающие любой исходящий и входящий трафик по любому протоколу с любых IP-адресов.

            {% note alert %}

            Для корректной работы кластера {{ mgp-name }} необходимо наличие хотя бы в одной из его групп безопасности правил, разрешающих любой входящий и исходящий трафик с любых IP-адресов.

            {% endnote %}

        * Выберите зону доступности и подсеть для размещения кластера. Чтобы создать новую подсеть, нажмите кнопку **Создать новую** рядом с нужной зоной доступности.
        * Выберите опцию **Публичный доступ**, чтобы подключаться к кластеру из интернета.
    1. Укажите имя пользователя и пароль.

        {% include [username-and-password-limits](../../_includes/mdb/mgp/note-info-user-name-and-pass-limits.md) %}

    1. При необходимости задайте дополнительные настройки кластера:

        {% include [Дополнительные настройки кластера](../../_includes/mdb/mgp/extra-settings.md) %}

    1. Укажите параметры хостов-мастеров на вкладке **Master**. Рекомендуемую конфигурацию см. в разделе [Расчет конфигурации кластера](calculate-specs.md#master).
    
        * Класс хоста — определяет технические характеристики виртуальных машин, на которых будут развернуты хосты-мастеры кластера.
        * В блоке **Хранилище**:
           * Выберите [тип хранилища](../concepts/storage.md).
           
             {% include [storages-step-settings](../../_includes/mdb/settings-storages-no-broadwell.md) %}

           * Выберите объем хранилища, который будет использоваться для данных.

    1. Укажите параметры хостов-сегментов на вкладке **Segment**. Рекомендуемую конфигурацию см. в разделе [Расчет конфигурации кластера](calculate-specs.md#segment).

        * Количество хостов-сегментов.
        * Количество сегментов на хост. Максимальное значение этого параметра зависит от класса хостов.
        * Класс хоста — определяет технические характеристики виртуальных машин, на которых будут развернуты хосты-сегменты кластера.
        * В блоке **Хранилище**:
           * Выберите [тип хранилища](../concepts/storage.md).
           
             {% include [storages-step-settings](../../_includes/mdb/settings-storages-no-broadwell.md) %}

           * Выберите объем хранилища, который будет использоваться для данных.

    1. Нажмите кнопку **Создать кластер**.

{% endlist %}

{% include [greenplum-trademark](../../_includes/mdb/mgp/trademark.md) %}