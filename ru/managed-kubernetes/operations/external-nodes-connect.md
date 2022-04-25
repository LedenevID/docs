# Подключение внешних узлов к кластеру

{% note info %}

Подключение [внешних узлов](../concepts/external-nodes.md) к [кластеру](./index.md#kubernetes-cluster) {{ managed-k8s-name }} находится на стадии [Preview](../../overview/concepts/launch-stages.md). Их использование не тарифицируется.

{% endnote %}

Внешние серверы подключаются в виде узлов к кластеру {{ managed-k8s-name }} с помощью специальных ресурсов {{ k8s }} API. Определения ([CustomResourceDefinitions](https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions)) этих ресурсов автоматически предустановленны в кластер.

## Требования для подключения внешних узлов к кластеру {#requirements}

Чтобы подключить внешние узлы к кластеру {{ managed-k8s-name }}, необходимо соответствие кластера и подключаемых серверов [определенным требованиям](../concepts/external-nodes.md#requirements).

## Обзор подключения

Для подключения внешних узлов необходимо [создать объект группы узлов](#node-group-create) в {{ k8s }} API кластера, к котором осуществляется подключение.

После создания объекта группы вы можете [добавлять узлы](#add-node) в кластер и [удалять узлы](#remove-node) из кластера.

При возникновении проблем с подключением, обратитесь к разделу [Диагностика проблем](external-nodes-connect.md#troubleshooting).

## Создание группы узлов {#node-group-create}

{% list tabs %}

- Консоль управления

  1. На странице кластера {{ k8s }} перейдите на **Управление узлами**.
  1. Нажмите кнопку **Создать группу узлов**, затем **Внешняя**.
  1. Введите имя группы узлов.
  1. Нажмите кнопку **Добавить**.

- CLI

  Создайте в {{ k8s }} API кластера, к котором осуществляется подключение, объект типа `NodeGroup` группы API `mks.yandex.cloud/v1alpha1` в [пространстве имен](./index.md#namespace) `yandex-system`:

  ```yaml
  apiVersion: mks.yandex.cloud/v1alpha1
  kind: NodeGroup
  metadata:
    name: external-node-group
    namespace: yandex-system
  ```

{% endlist %}

## Добавление узлов в кластер {#add-node}

{% list tabs %}

- Консоль управления

  1. На странице кластера {{ k8s }} перейдите на вкладку **Управление узлами**.
  1. Выберите нужную группу узлов.
  1. Нажмите кнопку **Редактировать**.
  1. Укажите адрес подключаемого сервера, доступный из облачной сети кластера.
  1. При необходимости нажмите на кнопку **Добавить IP-адрес**, чтобы добавить еще адреса.
  1. Нажмите **Сохранить**.

- CLI

  Укажите в спецификации объекта группы узлов IP адреса подключаемых серверов, доступные из облачной сети кластера:
  
  ```bash
  kubectl -n yandex-system edit nodegroup external-node-group
  ```

  >Пример:
  >
  >```yaml
  >apiVersion: mks.yandex.cloud/v1alpha1
  >kind: NodeGroup
  >metadata:
  >  name: external-node-group
  >  namespace: yandex-system
  >spec:
  >  ips: # перечислите IP-адреса подключаемых серверов, доступные из облачной сети кластера.
  >  - 10.130.0.4
  >  - 10.130.1.5
  >```

{% endlist %}

После этого необходимо [установить на подключаемые серверы системные компоненты](#node-setup).

После установки системных компонент серверы начнут подключение к кластеру.

Подключение узлов к кластеру {{ managed-k8s-name }} закончено, когда в кластере становятся доступны новые узлы в состоянии `Ready`:

{% list tabs %}

- Консоль управления

  1. Перейдите к подробностям соответствующей группы узлов.
  1. Выберите вкладку **Узлы**.

- CLI

  ```bash
  kubectl get node -o wide -w

  NAME       STATUS  ROLES   AGE    VERSION  INTERNAL-IP  EXTERNAL-IP  OS-IMAGE            KERNEL-VERSION    CONTAINER-RUNTIME
  ...
  ext-node2  Ready   <none>  4m03s  v1.20.6  10.130.0.4   <none>       Ubuntu 20.04.3 LTS  5.4.0-42-generic  docker://20.10.8
  ext-node1  Ready   <none>  4m25s  v1.20.6  10.130.1.5   <none>       Ubuntu 20.04.3 LTS  5.4.0-42-generic  docker://20.10.8
  ```

{% endlist %}

### Установка системных компонент на подключаемые серверы {#node-setup}

Для установки системных компонент и добавления узлов в кластер можно воспользоваться одним из двух способов:
* [автоматическая установка](#automatic-setup);
* [полуавтоматическая установка](#semi-automatic-setup).

#### Автоматическая установка {#automatic-setup}

Для автоматической установки необходимо создать в кластере секрет содержащий приватный SSH ключ для подключения к серверам. Создайте секрет:

```bash
kubectl -n yandex-system create secret generic <имя секрета> --from-file=ssh-privatekey=<путь к файлу> --type=kubernetes.io/ssh-auth
```

В спецификации ресурса `NodeGroup` укажите имя соответствующего секрета:

{% list tabs %}

- Консоль управления

  1. Перейдите к подробностям соответствующей группы узлов.
  1. Нажмите **Редактировать**.
  1. Выберите в выпадающем списке нужный секрет.
  1. Нажмите **Сохранить**.

- CLI

  ```bash
  kubectl -n yandex-system edit nodegroup external-node-group
  ```

  ```yaml
  apiVersion: mks.yandex.cloud/v1alpha1
  kind: NodeGroup
  metadata:
    name: external-node-group
    namespace: yandex-system
  spec:
    ips:
    ...
    provisionBySsh:
      sshKeySecret: <имя секрета>
  ```

{% endlist %}

На всех внешних узлах должна быть доступна возможность подключения с логином `root` и указанным SSH-ключом.

#### Полуавтоматическая установка {#semi-automatic-setup}

Для полуавтоматической установки необходимо установить на все внешние узлы базовый компонент и конфигурацию, обеспечивающие дальнейшую установку системных компонент.

1. После создания объекта NodeGroup в кластере становится доступен секрет, содержащий `kubeconfig` для использования на подключаемых серверах, получите его с помощью `kubectl`, настроенного на работу с кластером, и сохраните его в файл:

   ```bash
   kubectl -n yandex-system get secret <имя объекта NodeGroup>-maintainer-kube-config -o json | jq -r '.data."kube-config"' | base64 -d
   ```

1. Сохраните полученный `kubeconfig` на подключаемом сервере:

   ```bash
   sudo mkdir -p /etc/yandex-maintainer
   sudo vi /etc/yandex-maintainer/kube.config # Сохраните в этот файл содержимое `kubeconfig`, полученное на предыдущем шаге.
   ```

1. Выполните следующие команды на подключаемом сервере:

   ```bash
   sudo mkdir -p /home/kubernetes/bin
   sudo curl -o /home/kubernetes/bin/maintainer https://storage.yandexcloud.net/mk8s-maintainer/v1/maintainer
   sudo chmod +x /home/kubernetes/bin/maintainer
   sudo /home/kubernetes/bin/maintainer install
   ```

## Удаление внешних узлов из кластера {#remove-node}

{% list tabs %}

- Консоль управления

  1. Перейдите к подробностям соответствующей группы узлов.
  1. Нажмите **Редактировать**.
  1. Удалите IP адреса нужных узлов.
  1. Нажмите **Сохранить**.

- CLI

  Для отключения узлов удалите их IP адреса из поля `spec.ips` ресурса`NodeGroup`:

  ```bash
  kubectl -n yandex-system edit nodegroup
  ```

{% endlist %}

## Диагностика проблем {#troubleshooting}

При возникновении проблем в первую очередь обратитесь к событиям в пространстве имен `yandex-system`:

{% list tabs %}

- Консоль управления

  1. На странице кластера перейдите на вкладку **События**.
  1. Выберите пространство имен `yandex-system`.

- CLI

  ```bash
  kubectl -n yandex-system get events
  ```

{% endlist %}

Если информации недостаточно, обратитесь к логам системных компонент на соответствующем подключаемом сервере:

```bash
journalctl -u yandex-maintainer
journalctl -u kubelet
```

Учитывайте [требования](../concepts/external-nodes.md#requirements) для подключения внешних узлов.