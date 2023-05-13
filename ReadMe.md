# Управление Kubernetes (K8s)

Цель настоящего репозитория - сформировать перечень команд,
используемых наиболее часто, для управления кластером K8s.

---

## Kubectl

[Kubectl](https://kubernetes.io/ru/docs/reference/kubectl/overview/) — это инструмент командной строки для управления кластерами Kubernetes.
При запуске, `kubectl` ищет файл config в директории $HOME/.kube
Можно указать путь к другим файлам [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), используя переменную окружения `KUBECONFIG` или флаг [`--kubeconfig`](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/).

---

## Ресурсы и их псевдонимы

В таблице приведены только часто применимые компоненты, подробнее [здесь](https://kubernetes.io/ru/docs/reference/kubectl/overview/#типы-ресурсов)

| Resource Name            | Short Names |
| ------------------------ | ----------- |
| configmaps               | cm          |
| cronjobs                 | cj          |
| daemonsets               | ds          |
| deployments              | deploy      |
| endpoints                | ep          |
| events                   | ev          |
| horizontalpodautoscalers | hpa         |
| ingresses                | ing         |
| jobs                     | job         |
| namespaces               | ns          |
| nodes                    | no          |
| persistentvolumeclaims   | pvc         |
| persistentvolumes        | pv          |
| pods                     | po          |
| podsecuritypolicies      | psp         |
| replicasets              | rs          |
| secrets                  |             |
| serviceaccounts          | sa          |
| services                 | svc         |
| statefulsets             | sts         |
| storageclasses           | sc          |

Для вывода информации по всем ресурсам текущего кластера используется команда `kubectl api-resources`

---

## Типовые команды

```shell

# -> Namespace
# ------------------------------------------------------------------
# список пространств
kubectl get ns
# ------------------------------------------------------------------


# -> Pod
# ------------------------------------------------------------------

# список всех подов + фильтр по вхождению слова <word>
kubectl get po -A | grep <word>
kubectl get po -A -o wide | grep <word>

# Вывести все поды на узле
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=<node-name>

# список подов внутри пространства
kubectl -n <namespace> get po

# детализация пода | команда покажет все контейнеры внутри
kubectl -n <namespace> describe po <pod-name>

# вывод событий пода в реальном времени
kubectl -n <namespace> logs -f <pod-name>
# вывод событий пода с построчной прокруткой
kubectl -n <namespace> logs <pod-name> | less
# вывод событий пода с построчной прокруткой для контейнера app
kubectl -n <namespace> logs <pod-name> -c app | less
# вывод событий пода с построчной прокруткой и ошибкой 500 для контейнера app
kubectl -n <namespace> logs <pod-name> -c app | grep 500 | less

# Получить вывод команды 'date' в поде <pod-name>. По умолчанию отображается вывод из первого контейнера.
kubectl -n <namespace> exec <pod-name> date
# Получить вывод из запущенной команды 'date' в контейнере <container-name> пода <pod-name>.
kubectl -n <namespace> exec <pod-name> -c <container-name> date
# Получить интерактивный терминал (TTY) и запустить /bin/bash в поде <pod-name>.
# По умолчанию отображается вывод из первого контейнера.
kubectl -n <namespace> exec -ti <pod-name> /bin/bash
kubectl -n <namespace> exec --stdin --tty <pod-name> -- /bin/bash
kubectl -n <namespace> exec -it <pod-name> -- /bin/bash
# ------------------------------------------------------------------


# -> Deployment
# ------------------------------------------------------------------
#
# список всех деплойментов внутри пространства
kubectl -n <namespace> get deploy
#
kubectl -n <namespace> get deploy <deploy-name>
#
kubectl -n <namespace> describe deploy <deploy-name>
#
kubectl get deployment -A -o yaml
#
kubectl get deploy -A | grep worker-export
# ------------------------------------------------------------------


# -> Service
# ------------------------------------------------------------------
#
# список всех сервисов внутри пространства
kubectl -n <namespace> get svc
# детализация сервиса
kubectl -n <namespace> describe svc <service-name>
# ------------------------------------------------------------------


# -> Ingress
# ------------------------------------------------------------------
#
# список всех ингресов
kubectl get ing -A -o wide
# список ингресов внутри пространства
kubectl -n <namespace> get ingress
# манифест ингреса
kubectl -n <namespace> get ing <ingress-name> -o yaml
# детализация ингреса
kubectl -n <namespace> describe ingress <ingress-name>
# ------------------------------------------------------------------


# -> Job
# ------------------------------------------------------------------
#
# список заданий внутри пространства
kubectl -n <namespace> get job
kubectl -n <namespace> get jobs.batch
#
kubectl -n <namespace> delete job <job-name>
# ------------------------------------------------------------------


# -> CronJob  |  Создают наборы Job-ов
# ------------------------------------------------------------------
#
# список всех CronJob
kubectl get cronjob -A
# список всех CronJob внутри пространства
kubectl -n <namespace> get cronjob
#
kubectl -n <namespace> get cronjob <job-name>
NAME           SCHEDULE       SUSPEND   ACTIVE   LAST SCHEDULE   AGE
job-name       3,33 * * * *   False     3        3m40s           2y197d
#
kubectl -n <namespace> patch cronjobs <job-name> -p '{"spec" : {"suspend" : true }}'
kubectl -n <namespace> get cronjob <job-name>
NAME           SCHEDULE       SUSPEND   ACTIVE   LAST SCHEDULE   AGE
job-name       3,33 * * * *   True      3        6m45s           2y197d
#
kubectl -n <namespace> delete cronjob <job-name>
# ------------------------------------------------------------------


# -> ConfigMap
# ------------------------------------------------------------------
#
# список всех configmap внутри пространства
kubectl -n <namespace> get configmap
# Отобразить содержимое
kubectl -n <namespace> describe configmap <configmap-name>
kubectl -n <namespace> describe configmaps/<configmap-name>
# Открывает редактор для изменения
kubectl -n <namespace> edit configmap <configmap-name>
# ------------------------------------------------------------------


# -> Top
# ------------------------------------------------------------------
#
# Использование ресурсов (CPU/Memory/Storage) для каждого пода в пространстве
kubectl -n <namespace> top pod
#
# Использование ресурсов (CPU/Memory/Storage) для каждого узла
kubectl top node
# ------------------------------------------------------------------


# -> Event
# ------------------------------------------------------------------
#
kubectl get event --namespace <namespace> --field-selector involvedObject.name=<pod-name>
kubectl get events --output json
# ------------------------------------------------------------------


# -> Node
# ------------------------------------------------------------------
#
kubectl get nodes --show-labels
#
kubectl describe nodes <node-name>
#
kubectl edit node <node-name>
# ------------------------------------------------------------------


# -> DaemonSet
# ------------------------------------------------------------------
#
# список всех DaemonSet внутри пространства
kubectl -n <namespace> get ds
# Отобразить содержимое DaemonSet в формате Yaml
kubectl -n <namespace> get ds <daemonset-name> -o yaml
# ------------------------------------------------------------------


# Plugins
# ------------------------------------------------------------------
# Посмотреть все доступные плагины kubectl можно с помощью подкоманды kubectl plugin list:
kubectl plugin list
..
The following compatible plugins are available:
/usr/local/bin/kubectl-cert_manager
..
kubectl -n <namespace> get certificaterequests
kubectl -n cert-manager logs <pod-name>
kubectl cert-manager renew -n <namespace> ???
kubectl cert-manager renew -n <namespace> php-domains-ru
kubectl -n <namespace> get certificate
kubectl -n <namespace> delete certificaterequests php-domains-ru-lr45x
kubectl -n cert-manager get po -o wide
# ------------------------------------------------------------------
```

## Временно отключение на запуск пода (через скейлинг деплоймента)

```shell
# Смотрим какие есть деплойменты для воркеров
kubectl -n <namespace> get deploy

# Потушить воркер
kubectl -n <namespace> scale --replicas=0 deployment/worker-order
# или
kubectl -n <namespace> scale --replicas=0 deployment worker-order

# Запустить воркер
kubectl -n <namespace> scale --replicas=1 deployment/worker-order

# Смотрим какие есть деплойменты для воркеров
kubectl -n <namespace> get deploy
NAME                                     READY   UP-TO-DATE   AVAILABLE   AGE
worker-order                             1/1     1            1           64d
```

## Вывод из управления узла (воркера) и его возврат

```shell
# Смотрим топ загрузки каждой ноды и выбираем менее загруженную
kubectl top node

# Далее необходимо вывести одну ноду из кластера
kubectl drain <node-name>

# Заходим на сервер и выключаем его
poweroff

# Включаем и подключаем обратно в кластер
kubectl uncordon <node-name>

# Обязательно проверяем, что нода в статусе ready
kubectl get node -o wide | grep <node-name>
```


```shell
kubectl -n <namespace> edit configmap <configmap-name>

# Т.е. чтобы переселить поды нужно, нужно узлы "закордонить" конкретные узлы
kubectl cordon <node-name>

# Вывести список всех подов на каждом узле с сортировкой по узлам
kubectl -n <namespace> get pod -o wide --sort-by=.spec.nodeName

# Выбрать лишние поды и удалить, при это они автоматом запустятся на доступных узлах
kubectl -n <namespace> delete pod <pod-name>

# Восстанавливаем управление планировщиком для всех узлов
kubectl uncordon <node-name>
```

