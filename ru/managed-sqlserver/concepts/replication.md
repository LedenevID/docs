# Репликация

В многохостовых кластерах {{ mms-name }} [редакции](./index.md) **Enterprise Edition** используются _группы доступности_ (availability groups) Always On архитектуры High Availability для обеспечения отказоустойчивости.

В таких кластерах:

* Автоматически выбирается _первичная реплика_ (роль `MASTER`), которая принимает все запросы на чтение и запись. Остальные реплики становятся _вторичными_ (роль `REPLICA`).
* Используется _синхронная репликация_: транзакция подтверждается тогда, когда журнал изменений записан в хранилище и на первичной реплике, и на всех вторичных.
    На всех репликах используется [режим синхронной фиксации транзакций](https://docs.microsoft.com/ru-ru/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2016#availability-modes) (synchronous-commit-mode).
    Если все реплики, кроме первичной, становятся недоступны, то репликация не работает. Когда одна из вторичных реплик становится доступной, процесс репликации возобновляется.
* Если первичная реплика выходит из строя, то в кластере происходит [обработка отказа](https://docs.microsoft.com/ru-ru/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2016#types-of-failover) (automatic failover): роль первичной реплики переходит на одну из вторичных.

## Читаемые и нечитаемые реплики {#readable-and-non-readable-replicas}

По умолчанию вторичные реплики в {{ mms-name }} создаются нечитаемыми — подключаться к кластеру можно только через первичную реплику, которая обрабатывает запросы и на чтение, и на запись. Cделать вторичные реплики читаемыми можно [при создании кластера](../operations/cluster-create.md) или [изменении его настроек](../operations/update.md).

Использование читаемых реплик значительно снижает нагрузку по обработке операций чтения с первичной реплики, но обладает рядом особенностей:

* За использование каждой читаемой реплики взимается дополнительная плата, т. к. для таких реплик [требуются лицензии на ПО](../pricing/index.md#license).
* Между первичной и вторичной репликами возникает [задержка](https://docs.microsoft.com/ru-ru/sql/database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups?view=sql-server-2016#data-latency). Она обусловлена тем, что на применение изменений из журнала изменений на вторичной реплике требуется некоторое время, в течение которого состояние базы на вторичной реплике будет отличаться от состояния базы на первичной. Если важна консистентность читаемых данных, [подключайтесь](../operations/connect.md) к кластеру только через первичную реплику.

Подробнее см. в [документации {{ MS }}](https://docs.microsoft.com/ru-ru/sql/database-engine/availability-groups/windows/active-secondaries-readable-secondary-replicas-always-on-availability-groups?view=sql-server-2016).