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
| replicationcontrollers   | rc          |
| resourcequotas           | quota       |
| secrets                  |             |
| serviceaccounts          | sa          |
| services                 | svc         |
| statefulsets             | sts         |
| storageclasses           | sc          |

Для вывода информации по всем ресурсам текущего кластера используется команда `kubectl api-resources`

---

## Список типовых команд и их описание

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

# список подов внутри пространства
kubectl -n <namespace> get po

# Вывести все поды на узле
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=<node>
kubectl get pods --all-namespaces -o wide --field-selector spec.nodeName=k8s-prod

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

