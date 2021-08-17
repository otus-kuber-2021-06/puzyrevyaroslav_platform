# puzyrevyaroslav_platform
puzyrevyaroslav Platform repository
## <b>Homeworks</b>
<details>
<summary>Homework 1 kubernetes-intro</summary>
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
</details>
<details>
<summary>Homework 2 kubernetes-controlers</summary>
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
</details>
<details>
<summary>Homework 3 kubernetes-security</summary>

1. task01
```
kubectl apply -f bob-and-dave.yaml
```
2. task02
```
kubectl apply -f carol-and-other.yaml
```
3. task03
```
kubectl apply -f jane-and-ken.yaml
```
</details>
<details>
<summary>Homework 4 kubernetes-network</summary>
Выполнено домашнее задание, а также задание со звёздочкой.

Описание заданий со звёздочкой:

1. Были созданые 2 сервиса для tpc и udp протоколов 53го порта:

```
kubernetes-networks/coredns/coredns.yaml
```
2. Для доступности дэшборда k8s был добавлен ингресс с дополнительными аннтотациями от nginx:

```yaml
...
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/backend-protocol: "HTTPS"
...
```
3. Для канареечного релиза была использована копия прошлых манифесто с небольшими изменениями в аннотациях от nginx.

```yaml
...
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/canary: "true"  
    nginx.ingress.kubernetes.io/canary-weight: "50"
...
```
</details>
<details>
<summary>Homework 5 kubernetes-volumes</summary>

1. Был поднят minio из sts манифеста предоставленного в ДЗ.

2. Для задания со ⭐ был добавлен Secret в манифест, внутри которого данные  типа data автоматически энкодятся в строку при разворачивании pod из манифеста.

</details>
<details>
<summary>Homework 6 kubernetes-templating</summary>
Домашнее задание выполнено на платформе YandexCloud. Используется helm v3.6.3.

# cert-manager
Для установки cert-manager согласно документации необходимо установить дополнительные CDR:
```
kubectl apply -f https://github.com/jetstack/cert-manager/releases/download/v1.4.0/cert-manager.crds.yaml
```

Для chartmuseum указаны корректнсые ip адреса, с развёрнутого nginx-ingress и был установлен без проблем, с валидными сертификатами от acme.

# chartmuseum ⭐

В работе с chartmuseum нет абсолютно ничего сложного
```bash
helm repo add %reponame% %repourl%
cd chart/
helm package .
curl --data-binary "@chart-%version%.tgz" http://%repourl%:%port%/api/charts
```
*также можно использовать специальный плагин для helm*
```
helm push chart/ %reponame%
```
, кроме основных настроек: для того, чтобы работать с api необходимо при применении чарта указать параметр с слудующим значением:
```yaml
  DISABLE_API: false
```
# helmfile ⭐
Файл описан по пути заданному в описании задания:
```
```
Добавлены:
```yaml
- chart: stable/nginx 
  version: 1.41.3
- chart: jetstack/cert-manager 
  version: 2.13.2
- chart: harbor/harbor
  version: 1.1.2
```
с обязательным изменением expose.ingress.hosts.core для harbor, иначе он работать не будет. :)

# Создаём свой helm chart
*Так как в задании не указано включать .tgz файлы в .gitignore, а вес файлов очень маленький- они были оставленные для наглядности.*

Добавлены зависимости в Chart.yaml:
```yaml
...
dependencies:
- name: frontend
  version: 0.1.0
  repository: "file://../frontend"
```
# Создаём свой helm chart ⭐
Добавлена зависимость в Chart.yaml:
```yaml
...
- name: redis
  version: "14.8.7"
  repository: "@bitnami"
```
Также в values.yaml добавлена переменная:
```yaml
# for possible connect hipster-shop with standalone redis
redisCart: redis-cart-headless:6379
```
# Работа с helm-secrets 
При генерации gpg-ключей не возникло никаких проблем, также при шифровании секрета не было никаких сложностей.
После установки просмотр секрета методом base64 -d показал корректное значение: hiddenValue%.
# Kustomize
Все файлы и манифесты по примеру из лекции были положены в директорию kustomize.
</details>
</details>
<details>
<summary>Homework 7 kubernetes-operators</summary>

1. Собран и запушен в публичный registry docker образ из исходников предоставленных в ДЗ.

2. Добавлены предоставленные манифесты в папку deploy.

p.s. работа оператора нестабильна, но вычислить в чём проблема не смог.
</details>
<details>
<summary>Homework 8 kubernetes-monitoring</summary>

1. Взят дефолтный nginx вместе с nginx-prometheus-exporter, куда конфигмапом прокинут default.conf, указанный в конце deployment.yaml;
2. Prometheus Operato установлен из helm chart:
```bash
helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
helm repo update
helm upgrade --install kube-prometheus-stack prometheus-community/kube-prometheus-stack
```
3. Применёны манифесты deployment.yaml, service.yaml, servicemonitor.yaml:
```bash
kubectl apply -f kubernetes-monitoring/
```
4. Метрики отображаются в Prometheus.
</details>