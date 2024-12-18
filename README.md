# Домашнее задание к занятию «Базовые объекты K8S»

### Цель задания

В тестовой среде для работы с Kubernetes, установленной в предыдущем ДЗ, необходимо развернуть Pod с приложением и подключиться к нему со своего локального компьютера. 

------

### Чеклист готовности к домашнему заданию

1. Установленное k8s-решение (например, MicroK8S).
2. Установленный локальный kubectl.
3. Редактор YAML-файлов с подключенным Git-репозиторием.

------

### Инструменты и дополнительные материалы, которые пригодятся для выполнения задания

1. Описание [Pod](https://kubernetes.io/docs/concepts/workloads/pods/) и примеры манифестов.
2. Описание [Service](https://kubernetes.io/docs/concepts/services-networking/service/).

------

### Задание 1. Создать Pod с именем hello-world

1. Создать манифест (yaml-конфигурацию) Pod.

[hw.yml](https://github.com/stepynin-georgy/hw_k8s_2/blob/main/hw.yml)

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-hw
spec:
  containers:
  - name: echoserver
    image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    ports:
    - containerPort: 8080
```

2. Использовать image - gcr.io/kubernetes-e2e-test-images/echoserver:2.2.

Запуск пода:

```
user@k8s:/opt/hw_k8s_2$ microk8s kubectl apply -f /opt/hw_k8s_2/hw.yml
pod/pod-hw created
```

```
user@k8s:~$ microk8s kubectl get pods
NAME     READY   STATUS    RESTARTS   AGE
pod-hw   1/1     Running   0          20h
```

3. Подключиться локально к Pod с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

```
user@k8s:/opt/hw_k8s_2$ microk8s kubectl port-forward -n default pod/pod-hw 8080:8080 --address 0.0.0.0
Forwarding from 0.0.0.0:8080 -> 8080

user@k8s:~$ curl 127.0.0.1:8080


Hostname: pod-hw

Pod Information:
        -no pod information available-

Server values:
        server_version=nginx: 1.12.2 - lua: 10010

Request Information:
        client_address=127.0.0.1
        method=GET
        real path=/
        query=
        request_version=1.1
        request_scheme=http
        request_uri=http://127.0.0.1:8080/

Request Headers:
        accept=*/*
        host=127.0.0.1:8080
        user-agent=curl/8.5.0

Request Body:
        -no body in request-

user@k8s:~$
```

![изображение](https://github.com/stepynin-georgy/hw_k8s_2/blob/main/img/Screenshot_140.png)

------

### Задание 2. Создать Service и подключить его к Pod

1. Создать Pod с именем netology-web.
2. Использовать image — gcr.io/kubernetes-e2e-test-images/echoserver:2.2.
3. Создать Service с именем netology-svc и подключить к netology-web.
4. Подключиться локально к Service с помощью `kubectl port-forward` и вывести значение (curl или в браузере).

[service-hw.yml](https://github.com/stepynin-georgy/hw_k8s_2/blob/main/service-hw.yml)

```
apiVersion: v1
kind: Pod
metadata:
  name: pod-hw
  labels:
    app: pod-hw
spec:
  containers:
  - name: echoserver
    image: gcr.io/kubernetes-e2e-test-images/echoserver:2.2
    ports:
    - containerPort: 8080

---

apiVersion: v1
kind: Service
metadata:
  name: service-hw
spec:
  selector:
    app: service-hw
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

Запуск пода и сервиса:

```
user@k8s:/opt/hw_k8s_2$ microk8s kubectl apply -f service-hw.yml
pod/pod-hw configured
service/service-hw created
```

```
user@k8s:~$ microk8s kubectl get services
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.152.183.1     <none>        443/TCP   43h
service-hw   ClusterIP   10.152.183.200   <none>        80/TCP    22h
user@k8s:~$
```

Проверка:

```
user@k8s:~$ curl -G http://127.0.0.1:8085


Hostname: pod-hw

Pod Information:
        -no pod information available-

Server values:
        server_version=nginx: 1.12.2 - lua: 10010

Request Information:
        client_address=127.0.0.1
        method=GET
        real path=/
        query=
        request_version=1.1
        request_scheme=http
        request_uri=http://127.0.0.1:8080/

Request Headers:
        accept=*/*
        host=127.0.0.1:8085
        user-agent=curl/8.5.0

Request Body:
        -no body in request-

user@k8s:~$

```

![изображение](https://github.com/stepynin-georgy/hw_k8s_2/blob/main/img/Screenshot_141.png)

------

### Правила приёма работы

1. Домашняя работа оформляется в своем Git-репозитории в файле README.md. Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.
2. Файл README.md должен содержать скриншоты вывода команд `kubectl get pods`, а также скриншот результата подключения.
3. Репозиторий должен содержать файлы манифестов и ссылки на них в файле README.md.

------

### Критерии оценки
Зачёт — выполнены все задания, ответы даны в развернутой форме, приложены соответствующие скриншоты и файлы проекта, в выполненных заданиях нет противоречий и нарушения логики.

На доработку — задание выполнено частично или не выполнено, в логике выполнения заданий есть противоречия, существенные недостатки.
