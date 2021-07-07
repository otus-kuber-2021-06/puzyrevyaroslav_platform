# puzyrevyaroslav_platform
puzyrevyaroslav Platform repository
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

Работа окончена.