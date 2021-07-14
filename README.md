# puzyrevyaroslav_platform
puzyrevyaroslav Platform repository
## Homeworks
### Homework 1 kubernetes-intro
1. Испробовал функционал k9s, посмотрев документацию разобрался с причинами устойчивости munikube.
Добавил необходимые описания в PR into main from kubernetes-prepare.

    1.1 При билде nginx были сгенерирован следующий артифакт:
    
    - https://hub.docker.com/repository/docker/puzyrevyaroslav/nginx_otus_hw_1

Сгенерирован необходимый манифест *web-pod.yaml* , которые находится в корне проекта.

Запущены поды и отработана логика работы с port-forward, после была использована логика встроенная в k9s.

2. Склонирован репозиторий для приложения hipster, во избежания попадания ненужного репозитория в github- добавлен файл .gitignore, с прописаными исключениями.

Далее по заданию сгенерирован пустой yaml файл из --dry-run и после запуска были исправлены ошибки, путём добавления необхоидмых переменных (раз полная работоспособность не требовалась, то были скопированы из оригинального манифеста).

Артефакт после билда hipster-frontend:

- https://hub.docker.com/repository/docker/puzyrevyaroslav/hipster_frontend

### Homework 2 kubernetes-controlers
>Определите, что необходимо добавить в манифест:

Необходимо было добавить селектор следующего вида:
```
selector:
matchLabels:
    app: frontend
```
>Измените манифест таким образом, чтобы из манифеста сразу разворачивалось три реплики сервиса:

Необхоидмо изменить кол-во реплик: с 1 до 3.

>Почему обновление ReplicaSet не повлекло обновление запущенных pod?

ReplicaSet не отслеживает измненения конфигурации развёртывания, 
лишь поддерживает кол-во изначально сконфигурированных и запущенных подов.

### Deployment *

Для конфигурирования аналога blue-green развёртывания необходимо добавить секцию с использованием стратегии rollingUpdate, в параметры которой добавить конфигурацию:
```
...
rollingUpdate:
      maxSurge: 100%
      maxUnavailable: 0%
  ...
```
что начнёт удалять старые поды только после инициализации других с новой конфигурацией.

Для реализации Reverse Rolling Update необходима следующшая конфигурация:
```
...
    rollingUpdate:
      maxSurge: 0
      maxUnavailable: 1
  ...
```
которая позволит удалять старые поды только после инициализации нового пода, maxUnavailable: 1 разрешит делать это только по одному. 

### DaemonSet **

>Найдите способ модернизировать свой DaemonSet таким
образом, чтобы Node Exporter был развернут как на master, так и
на worker нодах:

Стандатный метод использованя DaemonSet позволяет разворачивать поды только на worker нодах. Происходит это из-за того, что у master и worker нод различные роли:
```
kubectl get nodes
NAME                  STATUS   ROLES                  AGE    VERSION
kind-control-plane    Ready    control-plane,master   97m   v1.21.1
kind-control-plane2   Ready    control-plane,master   97m   v1.21.1
kind-control-plane3   Ready    control-plane,master   96m   v1.21.1
kind-worker           Ready    <none>                 95m   v1.21.1
kind-worker2          Ready    <none>                 95m   v1.21.1
kind-worker3          Ready    <none>                 95m   v1.21.1
```

Для того, чтобы дать возможность поднимать поды на master нодах необходимо добавить следующие конфигурационные параметры:
```
...
  tolerations:
  - key: node-role.kubernetes.io/master
    operator: Exists
    effect: NoSchedule
  ...
```
где key: node-role.kubernetes.io/master позволит размешать поды на нодах с master ролью.