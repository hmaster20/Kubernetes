# Управление Kubernetes (K8s)

Цель настоящего репозитория - сформировать перечень команд,
используемых наиболее часто, для управления кластером K8s.

---

## Kubectl

[Kubectl](https://kubernetes.io/ru/docs/reference/kubectl/overview/) — это инструмент командной строки для управления кластерами Kubernetes.
При запуске, `kubectl` ищет файл config в директории $HOME/.kube
Можно указать путь к другим файлам [kubeconfig](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/), используя переменную окружения `KUBECONFIG` или флаг [`--kubeconfig`](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/).
