# MikhailGrabovskiy_platform
MikhailGrabovskiy Platform repository

## ДЗ №1

kube-apiserver восстанавливается системными механизмами.
core-dns реализован как Deployment с параметром replicas: 2.

- Развернул minikube.
- Выполнил шаги по проверке кластера.
- Создал докер-образ веб-сервера.
- Сделал yaml-манифест.
- С некоторым трудом пробросил порты и получил тестовую страницу.


## ДЗ №2

Основное ДЗ

### В процессе сделано:
Задание 1
Создать Service Account bob, дать ему роль admin в рамках всего кластера
Создать Service Account dave без доступа к кластеру
Задание 2
Создать Namespace prometheus
Создать Service Account carol в этом Namespace
Дать всем Service Account в Namespace prometheus возможность делать get, list, watch в отношении Pods всего кластера
Задание 3
Создать Namespace dev
Создать Service Account jane в Namespace dev
Дать jane роль admin в рамках Namespace dev
Создать Service Account ken в Namespace dev
Дать ken роль view в рамках Namespace dev

### Как запустить проект:
kubectl apply -f . --recursive

### Как проверить работоспособность:
kubectl auth can-i get deployments --as system:serviceaccount:default:bob
kubectl auth can-i get pods --as system:serviceaccount:default:dave

kubectl auth can-i get deployments --as system:serviceaccount:prometheus:carol
kubectl auth can-i list pods --as system:serviceaccount:prometheus:carol -n prometheus
kubectl auth can-i list pods --as system:serviceaccount:prometheus:carol

kubectl auth can-i get deployments --as system:serviceaccount:dev:jane
kubectl auth can-i create deployments --as system:serviceaccount:dev:jane -n dev
kubectl auth can-i get deployments --as system:serviceaccount:dev:ken
kubectl auth can-i get deployments --as system:serviceaccount:dev:ken -n dev
kubectl auth can-i create deployments --as system:serviceaccount:dev:ken -n dev


## ДЗ №3

Основное ДЗ

### В процессе сделано:
1. Сделал readiness и liveness проверки пода. Увидел эффекты корректной реализации и варианта с ошибкой.
2. Сделал Deployment с разным количеством реплик и вариантами роллаута.
3. Сделал сервис типа ClusterIP, просмотрел цепь сетевых правил.
4. Через dashboard перевел kube-proxy в режим IPVS, обновил правила.
5. Установил и настроил балансировщик MetalLB. Пробросил маршрут, в браузере получил тестовую страницу с чередованием подов.
6. Установил и настроил Ingress-контроллер, в браузере получил тестовую страницу с чередованием подов.

### Как запустить проект:
Пошагово применять манифесты и делать ручные действия по инструкции в ДЗ.

### Как проверить работоспособность:
По инструкции в ДЗ.


## ДЗ №4

Основное ДЗ

### В процессе сделано:
1. Установил kind.
2. Создал в кластере StatefulSet с хранилищем MinIO, PV связалось с PVC.
3. Создал сервис для доступа к MinIO по 9000 порту.


### Как запустить проект:
- Выполнить вложенные манифесты.

### Как проверить работоспособность:
- kubectl get statefulsets
- kubectl get pods
- kubectl get pvc
- kubectl get pv


## ДЗ №5

Основное ДЗ

### В процессе сделано:
1. Модифицировал конфигурацию кластера для работы со снапшотами
2. Установил CSI-драйвер
3. Создал StorageClass для CSI Host Path Driver, PVC и POD


### Как запустить проект:
1. Установить драйвер https://github.com/kubernetes-csi/csi-driver-host-path/blob/master/deploy
2. Применить манифесты:
- kubectl apply -f 01-csi-storageclass.yaml
- kubectl apply -f 02-csi-storagepvc.yaml
- kubectl apply -f 03-csi-storagepod.yaml

### Как проверить работоспособность:
- kubectl get pods
- kubectl get pvc
- kubectl get pv


## ДЗ №6

Основное ДЗ

### В процессе сделано:
1. Установил в кластер kubectl debug по инструкции https://github.com/aylei/kubectl-debug
2. Проверил и исправил версию образа агента для корректной работы команды strace на Web-сервере из ДЗ-1
3. Установил netperf-operator для диагностики сети между нодами


### Как запустить проект:
1. Выполнить инструкцию https://github.com/aylei/kubectl-debug
2. Применить манифесты:
- kubectl apply -f ./kit/kit-clusterrole.yaml
- kubectl apply -f ./kit/kit-serviceaccount.yaml
- kubectl apply -f ./kit/kit-clusterrolebinding.yaml
- kubectl apply -f ./kit/netperf-calico-policy.yaml
- kubectl apply -f ./kit/iptables-tailer.yaml

### Как проверить работоспособность:
- kubectl get events -A
- kubectl describe pod --selector=app=netperfoperator


## ДЗ №7

Основное ДЗ

### В процессе сделано:
1. Сделал Custom Resource definition для MySQL
2. Создал и заполнил БД тестовыми данными
3. Проверил работу CR

### Как запустить проект:
- kubectl apply -f service-account.yml
- kubectl apply -f role.yml
- kubectl apply -f role-binding.yml
- kubectl apply -f deploy-operator.yml
- kubectl apply -f crd.yml
- kubectl apply -f cr.yml

### Как проверить работоспособность:

- kubectl exec -it mysql-instance-6c76bcf945-mvw7b -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.

<pre><code>+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+</code></pre>



- kubectl get jobs.batch

NAME                         COMPLETIONS   DURATION   AGE

backup-mysql-instance-job    1/1           2s         53s

restore-mysql-instance-job   0/1           7m17s      7m17s


## ДЗ №10

Основное ДЗ

### В процессе сделано:
1. Установка tiller в namespace cert-manager. 
Попытка установки helm-chart cert-manager с ошибкой:
Error: release cert-manager failed: clusterroles.rbac.authorization.k8s.io is forbidden: User "system:serviceaccount:cert-manager:tiller-cert-manager" cannot create resource "clusterroles" in API group "rbac.authorization.k8s.io" at the cluster scope
Добавление ClusterIssuer и переинициализация tiller
Успешная установка cert-manager.
2. Установка chartmuseum с включенным ingress ресурсом в файле values.yaml
3. Установка harbor с использованием helm 3
4. Разделение на два релиза, параметризация и установка с зависимостями демо-приложения Socks shop.
5. Шаблонизация сервисов service и deployment с использованием Kubecfg
6. Параметризация микросервиса Card при помощи Kustomize для запуска в разных Namespace с префиксами и лейблами.

### Как запустить проект:
1. cert-manager
- kubectl apply -f 1-cert-manager-namespase.yaml	
- kubectl apply -f 2-cert-manager-service.yaml	
- kubectl apply -f 3-cert-manager-role.yaml	
- kubectl apply -f 4-cert-manager-rolebinding.yaml	
- helm repo add jetstack https://charts.jetstack.io
- kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release0.9/deploy/manifests/00-crds.yaml
- kubectl label namespace cert-manager certmanager.k8s.io/disable-validation="true"
- kubectl apply -f 5-cert-manager-clusterissuer.yaml
- helm init --service-account=tiller
- helm upgrade --install cert-manager jetstack/cert-manager --wait --namespace=cert-manager --version=0.9.0 --tiller-namespace cert-manager --atomic

2. chartmuseum
helm tiller run helm upgrade --install chartmuseum stable/chartmuseum --wait --namespace=chartmuseum --version=2.3.2 -f c:/k8s/hw10/chartmuseum/values.yaml

3. harbor
- kubectl apply -f harbor-namespase.yaml	
- helm install harbor harbor/harbor --wait --namespace=harbor --version=1.1.2 -f c:/k8s/hw10/harbor/values.yaml

4. Socks shop
helm upgrade --install socks-shop kubernetes-templating/socks-shop --namespace socks-shop

5. Kubecfg
kubecfg update kubecfg/services.jsonnet --namespace socks-shop

6. Kustomize
kubectl apply -k kustomize/overrides/socks-shop
kubectl create ns socks-shop-prod
kubectl apply -k kustomize/overrides/socks-shop-prod

### Как проверить работоспособность:
1. cert-manager
kubectl get all -n cert-manager

2. chartmuseum
https://chartmuseum.35.223.243.232.nip.io/

3. harbor
https://harbor.35.223.243.232.nip.io/

4. Socks shop
https://socks-shop.35.223.243.232.nip.io/

5. Kubecfg
kubecfg show kubecfg/services.jsonnet

6. Kustomize
kubectl get all -n socks-shop
kubectl get all -n socks-shop-prod


## ДЗ №11

Основное ДЗ

### В процессе сделано:
1. Установлен Consul и Vault в режиме high availability.

### Как запустить проект:
1. 

### Как проверить работоспособность:
1. 
