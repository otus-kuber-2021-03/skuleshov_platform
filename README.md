# skuleshov_platform
skuleshov Platform repository

## Домашнее задание 1
### Задание 1
Почему все pod в namespace kube-system восстановились после удаления?

### Решение
```
~ kubectl get pods -n kube-system
NAME                               READY   STATUS    RESTARTS   AGE
coredns-74ff55c5b-rplsw            1/1     Running   0          3h25m
etcd-minikube                      1/1     Running   0          3h25m
kube-apiserver-minikube            1/1     Running   0          3h25m
kube-controller-manager-minikube   1/1     Running   0          3h25m
kube-proxy-4rv7g                   1/1     Running   0          3h25m
kube-scheduler-minikube            1/1     Running   0          3h25m
storage-provisioner                1/1     Running   0          3h25m
```
Под coredns является ресурсом вида Deployment, т е
```
~ kubectl get deployments -n kube-system
NAME      READY   UP-TO-DATE   AVAILABLE   AGE
coredns   1/1     1            1           3h28m
```
Тогда посмотрев описание деплоймента:
```
~ kubectl describe deployments coredns -n kube-system
Увидим Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
```
Что и будет у нас поддерживаться контроллером если убить coredns

Остальные поды являются статическими подами которые непосредственно управляются демоном kubelet на ноде. Kubelet наблюдает за всеми своими статическими подами

### Задание 2
### 2.1
Был взят alpine image, на который был установлен nginx. Конфиг nginx приносится файлом из репозитория.
Собран image и залит в паблик докерхаба https://hub.docker.com/r/skuleshov/app_nginx (docker pull skuleshov/app_nginx:02)

### 2.2
Написан манифест web-pod.yaml.
Добавлен init контейнер для приноса index.html в volume /app. Если запустить под и пробросить порт командой kubectl port-forward --address 0.0.0.0 pod/web 8000:8000, то открывается страничка html

### 2.3
Задача с "звездочкой"
#### Выясните причину, по которой pod frontend находится в статусе Error.
Для запуска не хватает переменных окружения. Новый манифест frontend-pod-healthy.yaml с добавленными переменными запускается без ошибок.


## Домашнее задание 2
### 1.
Создан кластер kind

### 2. ReplicaSet
Создан и применен манифест frontend-replicaset.yaml. Исправлено описание ReplicaSet. Игры с replicas.

#### Почему обновление ReplicaSet не повлекло обновление запущенных pod?

Потому что контроллер репликасет не занимается переконфигурированием подов в реалтайме, он только поддерживает кол-во реплик пода.

### 3. Deployment
Собраны образы с тегами v0.0.1 и v0.0.2 и развернут под paymentService. Сделаны манифесты: paymentservice-replicaset.yaml, paymentservice-deployment.yaml, 

#### maxSurge и maxUnavailable
Аналог blue-green: paymentservice-deployment-bg.yaml

Reverse Rolling Update: paymentservice-deployment-reverse.yaml

### 4. Probes
Применен манифест с readinessProbe

### 5. DaemonSet
Сделан манифест node-exporter-daemonset.yaml метрики которого доступны по адресу localhost:9100/metrics при форварде kubectl port-forward <имя любого pod в DaemonSet> 9100:9100

Для запуска на воркерах и мастер нодах используем tolerations
```
tolerations:
  - operator: "Exists"
```



