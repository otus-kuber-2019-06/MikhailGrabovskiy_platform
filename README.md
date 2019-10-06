# MikhailGrabovskiy_platform
MikhailGrabovskiy Platform repository

ДЗ №1

kube-apiserver восстанавливается системными механизмами.
core-dns реализован как Deployment с параметром replicas: 2.

- Развернул minikube.
- Выполнил шаги по проверке кластера.
- Создал докер-образ веб-сервера.
- Сделал yaml-манифест.
- С некоторым трудом пробросил порты и получил тестовую страницу.


ДЗ №2

Основное ДЗ

В процессе сделано:
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

Как запустить проект:
kubectl apply -f . --recursive

Как проверить работоспособность:
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

PR checklist:
[Х] Выставлен label с номером домашнего задания


ДЗ №3

Основное ДЗ
ДЗ со звездочкой не выполнял.

В процессе сделано:
1. Сделал readiness и liveness проверки пода. Увидел эффекты корректной реализации и варианта с ошибкой.
2. Сделал Deployment с разным количеством реплик и вариантами роллаута.
3. Сделал сервис типа ClusterIP, просмотрел цепь сетевых правил.
4. Через dashboard перевел kube-proxy в режим IPVS, обновил правила.
5. Установил и настроил балансировщик MetalLB. Пробросил маршрут, в браузере получил тестовую страницу с чередованием подов.
6. Установил и настроил Ingress-контроллер, в браузере получил тестовую страницу с чередованием подов.

Как запустить проект:
Пошагово применять манифесты и делать ручные действия по инструкции в ДЗ.

Как проверить работоспособность:
По инструкции в ДЗ.

PR checklist:
[Х] Выставлен label с номером домашнего задания

ДЗ №4

Основное ДЗ

В процессе сделано:
1. Установил kind.
2. Создал в кластере StatefulSet с хранилищем MinIO, PV связалось с PVC.
3. Создал сервис для доступа к MinIO по 9000 порту.


Как запустить проект:
Выполнить вложенные манифесты.

Как проверить работоспособность:
kubectl get statefulsets
kubectl get pods
kubectl get pvc
kubectl get pv

PR checklist:
[Х] Выставлен label с номером домашнего задания

