# MikhailGrabovskiy_platform
MikhailGrabovskiy Platform repository

## ДЗ №1

kube-apiserver восстанавливается системными механизмами.
core-dns реализован как Deployment с параметром replicas: 2.

- Развернул minikube.
- Выполнил шаги по проверке кластера.
- Создал докер-образ веб-сервера.
- Сделал yaml-манифест.
- Пробросил порты и получил тестовую страницу.


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
2. Созданы секреты и политики
3. Настроена авторизация в vault через kubernetes sa
4. Запущен под с nginx, в который прокинуты секреты из vault через consul-template
5. Запущен CA на базе vault

helm status vault:
<pre><code>LAST DEPLOYED: Sat Dec 21 15:41:41 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ClusterRole
NAME                              AGE
vault-agent-injector-clusterrole  105s

==> v1/ClusterRoleBinding
NAME                          AGE
vault-agent-injector-binding  105s

==> v1/ConfigMap
NAME          AGE
vault-config  105s

==> v1/Deployment
NAME                  AGE
vault-agent-injector  105s

==> v1/Pod(related)
NAME                                  AGE
vault-0                               105s
vault-1                               105s
vault-2                               105s
vault-agent-injector-b8c466d88-9lhzh  105s

==> v1/Service
NAME                      AGE
vault                     105s
vault-agent-injector-svc  105s
vault-ui                  105s

==> v1/ServiceAccount
NAME                  AGE
vault                 105s
vault-agent-injector  105s

==> v1/StatefulSet
NAME   AGE
vault  105s

==> v1beta1/ClusterRoleBinding
NAME                  AGE
vault-server-binding  105s

==> v1beta1/MutatingWebhookConfiguration
NAME                      AGE
vault-agent-injector-cfg  105s

==> v1beta1/PodDisruptionBudget
NAME   AGE
vault  105s


NOTES:

Thank you for installing HashiCorp Vault!

Now that you have deployed Vault, you should look over the docs on using
Vault with Kubernetes available here:

https://www.vaultproject.io/docs/


Your release is named vault. To learn more about the release, try:

  $ helm status vault
  $ helm get vault</code></pre>
kubectl exec -it vault-0 -- vault operator init --key-shares=1 --key-threshold=1
<pre><code>Unseal Key 1: 48COdnZGpkDSY/oELBuBzQdcW+loRiT6DssbuvwfBRE=

Initial Root Token: s.q2lblFMzwTNUoVXbdzartXQ2

Vault initialized with 1 key shares and a key threshold of 1. Please securely
distribute the key shares printed above. When the Vault is re-sealed,
restarted, or stopped, you must supply at least 1 of these keys to unseal it
before it can start servicing requests.

Vault does not store the generated master key. Without at least 1 key to
reconstruct the master key, Vault will remain permanently sealed!

It is possible to generate new unseal keys, provided you have a quorum of
existing unseal keys shares. See "vault operator rekey" for more information.</code></pre>

- kubectl exec -it vault-0 -- vault operator unseal 48COdnZGpkDSY/oELBuBzQdcW+loRiT6DssbuvwfBRE=
- kubectl exec -it vault-1 -- vault operator unseal 48COdnZGpkDSY/oELBuBzQdcW+loRiT6DssbuvwfBRE=
- kubectl exec -it vault-2 -- vault operator unseal 48COdnZGpkDSY/oELBuBzQdcW+loRiT6DssbuvwfBRE=

helm status vault:
<pre><code>LAST DEPLOYED: Sat Dec 21 15:41:41 2019
NAMESPACE: default
STATUS: DEPLOYED

RESOURCES:
==> v1/ClusterRole
NAME                              AGE
vault-agent-injector-clusterrole  173m

==> v1/ClusterRoleBinding
NAME                          AGE
vault-agent-injector-binding  173m

==> v1/ConfigMap
NAME          AGE
vault-config  173m

==> v1/Deployment
NAME                  AGE
vault-agent-injector  173m

==> v1/Pod(related)
NAME                                  AGE
vault-0                               173m
vault-1                               173m
vault-2                               173m
vault-agent-injector-b8c466d88-9lhzh  173m

==> v1/Service
NAME                      AGE
vault                     173m
vault-agent-injector-svc  173m
vault-ui                  173m

==> v1/ServiceAccount
NAME                  AGE
vault                 173m
vault-agent-injector  173m

==> v1/StatefulSet
NAME   AGE
vault  173m

==> v1beta1/ClusterRoleBinding
NAME                  AGE
vault-server-binding  173m

==> v1beta1/MutatingWebhookConfiguration
NAME                      AGE
vault-agent-injector-cfg  173m

==> v1beta1/PodDisruptionBudget
NAME   AGE
vault  173m


NOTES:

Thank you for installing HashiCorp Vault!

Now that you have deployed Vault, you should look over the docs on using
Vault with Kubernetes available here:

https://www.vaultproject.io/docs/


Your release is named vault. To learn more about the release, try:

  $ helm status vault
  $ helm get vault</code></pre>

kubectl exec -it vault-0 -- vault login s.q2lblFMzwTNUoVXbdzartXQ2
<pre><code>Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                s.q2lblFMzwTNUoVXbdzartXQ2
token_accessor       IqNfkZaW3ocarF4wM1w25STS
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]</code></pre>

kubectl exec -it vault-0 -- vault auth list
<pre><code>Path      Type     Accessor               Description
----      ----     --------               -----------
token/    token    auth_token_36b4e877    token based credentials</code></pre>

- kubectl exec -it vault-0 -- vault secrets enable --path=otus kv
- kubectl exec -it vault-0 -- vault secrets list --detailed
- kubectl exec -it vault-0 -- vault kv put otus/otus-ro/config username='otus' password='asajkjkahs'
- kubectl exec -it vault-0 -- vault kv put otus/otus-rw/config username='otus' password='asajkjkahs'
- kubectl exec -it vault-0 -- vault read otus/otus-ro/config
<pre><code>Key                 Value
---                 -----
refresh_interval    768h
password            'asajkjkahs'
username            'otus'</code></pre>
- kubectl exec -it vault-0 -- vault kv get otus/otus-rw/config
<pre><code>====== Data ======
Key         Value
---         -----
password    'asajkjkahs'
username    'otus'</code></pre>

- kubectl exec -it vault-0 -- vault auth enable kubernetes
- kubectl exec -it vault-0 -- vault auth list
<pre><code>Path           Type          Accessor                    Description
----           ----          --------                    -----------
kubernetes/    kubernetes    auth_kubernetes_d1ffdd23    n/a
token/         token         auth_token_36b4e877         token based credentials</code></pre>

- kubectl create serviceaccount vault-auth
- kubectl apply --filename hw11/vault-auth-service-account.yml

- export VAULT_SA_NAME=$(kubectl get sa vault-auth -o jsonpath="{.secrets[*]['name']}")
- export SA_JWT_TOKEN=$(kubectl get secret $VAULT_SA_NAME -o jsonpath="{.data.token}" | base64 --decode; echo)
- export SA_CA_CRT=$(kubectl get secret $VAULT_SA_NAME -o jsonpath="{.data['ca\.crt']}" | base64 --decode; echo)
- export K8S_HOST=$(more ~/.kube/config | grep server |awk '/http/ {print $NF}')

- ### alternative way
- export K8S_HOST=$(kubectl cluster-info | grep ‘Kubernetes master’ | awk ‘/https/ {print $NF}’ | sed ’s/\x1b\[[0-9;]*m//g’ )

Цель конструкции sed ’s/\x1b\[[0-9;]*m//g’ -  removing ANSI color codes from text stream

- kubectl cp otus-policy.hcl vault-0:./tmp
- kubectl exec -it vault-0 -- vault policy write otus-policy /tmp/otus-policy.hcl
- kubectl exec -it vault-0 -- vault write auth/kubernetes/role/otus bound_service_account_names=vault-auth bound_service_account_namespaces=default policies=otus-policy ttl=24h

Создание пода с привязанным сервис аккаунтом и установим туда curl и jq:
- kubectl run --generator=run-pod/v1 tmp --rm -i --tty --serviceaccount=vault-auth --image alpine:3.7
- apk add curl jq

Залогинимся и получим клиентский токен: s.hMtSCm3bFKwTAbF8Y8j04qtS
- VAULT_ADDR=http://vault:8200
- KUBE_TOKEN=$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)
- curl --request POST --data '{"jwt": "'$KUBE_TOKEN'", "role": "otus"}' $VAULT_ADDR/v1/auth/kubernetes/login | jq
- TOKEN=$(curl -k -s --request POST --data '{"jwt": "'$KUBE_TOKEN'", "role": "test"}' $VAULT_ADDR/v1/auth/kubernetes/login | jq '.auth.client_token' | awk -F\" '{print $2}')

Проверка чтения:
- curl --header "X-Vault-Token:s.hMtSCm3bFKwTAbF8Y8j04qtS" $VAULT_ADDR/v1/otus/otus-ro/config
- curl --header "X-Vault-Token:s.hMtSCm3bFKwTAbF8Y8j04qtS" $VAULT_ADDR/v1/otus/otus-rw/config

Проверка записи:
- curl --request POST --data '{"bar": "baz"}' --header "X-Vault-Token:s.hMtSCm3bFKwTAbF8Y8j04qtS" $VAULT_ADDR/v1/otus/otus-ro/config
<pre><code>"errors":["1 error occurred:\n\t* permission denied\n\n"]</code></pre>
- curl --request POST --data '{"bar": "baz"}' --header "X-Vault-Token:s.hMtSCm3bFKwTAbF8Y8j04qtS" $VAULT_ADDR/v1/otus/otus-rw/config
<pre><code>"errors":["1 error occurred:\n\t* permission denied\n\n"]</code></pre>
- curl --request POST --data '{"bar": "baz"}' --header "X-Vault-Token:s.hMtSCm3bFKwTAbF8Y8j04qtS" $VAULT_ADDR/v1/otus/otus-rw/config1

Секрет config не обновился, т.к. в политике (capabilities) отсутствует update. Если добавить, то получится.
Секрет config1 успешно создался, т.к. новый

### Use case использования авторизации через кубер

- kubectl create configmap example-vault-agent-config --from-file=./vault-guides/identity/vault-agent-k8s-demo/configs-k8s/
- kubectl get configmap example-vault-agent-config -o yaml
<pre><code>apiVersion: v1
data:
  consul-template-config.hcl: "vault {\r\n  renew_token = false\r\n  vault_agent_token_file
    = \"/home/vault/.vault-token\"\r\n  retry {\r\n    backoff = \"1s\"\r\n  }\r\n}\r\n\r\ntemplate
    {\r\n  destination = \"/etc/secrets/index.html\"\r\n  contents = <<EOH\r\n  &lt;html>\r\n
    \ &lt;body>\r\n  &lt;p>Some secrets:&lt;/p>\r\n  {{- with secret \"otus/otus-ro/config\"
    }}\r\n  &lt;ul>\r\n  &lt;li>&lt;pre>username: {{ .Data.username }}&lt;/pre>&lt;/li>\r\n  &lt;li>&lt;pre>password:
    {{ .Data.password }}&lt;/pre>&lt;/li>\r\n  &lt;/ul>\r\n  {{ end }}\r\n  &lt;/body>\r\n  &lt;/html>\r\n
    \ EOH\r\n}\r\n"
  vault-agent-config.hcl: "# Uncomment this to have Agent run once (e.g. when running
    as an initContainer)\r\nexit_after_auth = true\r\npid_file = \"/home/vault/pidfile\"\r\n\r\nauto_auth
    {\r\n    method \"kubernetes\" {\r\n        mount_path = \"auth/kubernetes\"\r\n
    \       config = {\r\n            role = \"example\"\r\n        }\r\n    }\r\n\r\n
    \   sink \"file\" {\r\n        config = {\r\n            path = \"/home/vault/.vault-token\"\r\n
    \       }\r\n    }\r\n}\r\n"
kind: ConfigMap
metadata:
  creationTimestamp: "2019-12-21T21:06:47Z"
  name: example-vault-agent-config
  namespace: default
  resourceVersion: "2798685"
  selfLink: /api/v1/namespaces/default/configmaps/example-vault-agent-config
  uid: cccd8988-2435-11ea-be7f-42010a8000b3</code></pre>
- kubectl apply -f vault-guides/identity/vault-agent-k8s-demo/example-k8s-spec.yml --record

Смотрим файл index.html в контейнере nginx:
- kubectl exec -it vault-agent-example --container nginx-container sh
- cat usr/share/nginx/html/index.html

<pre><code>
  &lt;html&gt;
  &lt;body&gt;
  &lt;p&gt;Some secrets:&lt;/p&gt;
  &lt;ul&gt;
  &lt;li&gt;&lt;pre&gt;username: 'otus'&lt;/pre&gt;&lt;/li&gt;
  &lt;li&gt;&lt;pre&gt;password: 'asajkjkahs'&lt;/pre&gt;&lt;/li&gt;
  &lt;/ul&gt;
  &lt;/body&gt;
  &lt;/html&gt;
  </code></pre>


### CA на базе vault

Включить pki секреты
<pre><code>kubectl exec -it vault-0 -- vault secrets enable pki
kubectl exec -it vault-0 -- vault secrets tune -max-lease-ttl=87600h pki
kubectl exec -it vault-0 -- vault write -field=certificate pki/root/generate/internal common_name="exmaple.ru" ttl=87600h > CA_cert.crt</code></pre>

URL для CA и отозванных сертификатов
<pre><code>kubectl exec -it vault-0 -- vault write pki/config/urls issuing_certificates="http://vault:8200/v1/pki/ca" crl_distribution_points="http://vault:8200/v1/pki/crl"</code></pre>

Создадим промежуточный сертификат
<pre><code>kubectl exec -it vault-0 -- vault secrets enable --path=pki_int pki
kubectl exec -it vault-0 -- vault secrets tune -max-lease-ttl=87600h pki_int
kubectl exec -it vault-0 -- vault write -format=json
pki_int/intermediate/generate/internal \
common_name="example.ru Intermediate Authority" | jq -r '.data.csr' >
pki_intermediate.csr</code></pre>

Пропишем промежуточный сертификат в vault
<pre><code>kubectl cp pki_intermediate.csr vault-0:/tmp
kubectl exec -it vault-0 -- vault write -format=json pki/root/sign-intermediate \
csr=@/tmp/pki_intermediate.csr \
format=pem_bundle ttl="43800h" | jq -r '.data.certificate' > intermediate.cert.pem
kubectl cp intermediate.cert.pem vault-0:/tmp
kubectl exec -it vault-0 -- vault write pki_int/intermediate/set-signed certificate=@/tmp/intermediate.cert.pem</code></pre>

Создадим роль для выдачи сертификатов
<pre><code>kubectl exec -it vault-0 -- vault write pki_int/roles/example-dot-ru \
allowed_domains="example.ru" allow_subdomains=true max_ttl="720h"</code></pre>

Создадим и отзовем сертификат
<pre><code>kubectl exec -it vault-0 -- vault write pki_int/issue/example-dot-ru common_name="gitlab.example.ru" ttl="24h"
kubectl exec -it vault-0 -- vault write pki_int/revoke serial_number="22:69:ca:c4:1d:de:ae:45:57:31:03:19:f1:dc:69:e9:0d:51:8c:fa"
</code></pre>

Выдача при создании сертификата:
<pre><code>Key                 Value
---                 -----
ca_chain            [-----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUH5mwnTKCrFjBE7rykjDxaVjkG/owDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0xOTEyMjIxMTQyMDhaFw0yNDEy
MjAxMTQyMzhaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALHAP27OZevI
zt2fSy5W3vpVufbJu6+3daBsXoUxOaMWmkVm6ob1qoUxpm/WQLT+xhmndt7pv4pD
ACqUEKzHL2JTlXemvKAXBmXNYJWH60fcG6DgjtEh7ny/Og0pdXlKgEf4ItbQtMC5
dKz0G3YNaxj0OWdZWTGN1qDWQT3sECHtBv4YudPDwMCCJDeDR909AZSmDLay3C9s
OasXCO/R4PsST7JMkl4gjXktm20kaqMt95N/uYfh7Lkwcu95XrtA7F6EHWuiuCqT
EymTBaiUuskzsuNzh0KRe0THFlCNDL7W76SLAL+c8nmCszH7ylmOf/tnbKEJd5aQ
dBIPXBS9/RcCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUHmUVD8OeN5rjt4jyJhDBQgWyOjQwHwYDVR0jBBgwFoAU
TcOGNriRV5TqXnYybrgQbAk0JcYwNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
nwVjpLZG+JMmlY4Kb9yQb8m1Gq8whiH8qu7AZ142rduAYtILtJ9bFRBl6bXBI/lX
WCUi+u8hG4UVDr76j6H22W9I+YHIsRWIunm+/SCLxWbXDPbtrIy8OX7XmfCAV5YO
nvLimMKwZbzzdTZvPhkGXCpGY+D8xLByJH5alFzlak7bJx8CXISijMjNP+1RM8xn
ERgch+c6IRYwHTFyNlruikrdHwVB/Of0TnlQlVFAQ5q1lciks4JQLbkCO/Yhnw31
d+mKPW6lFDd+gHa4WPmwfIRL8irw0vb3ugHjDtQd+h7toBTY4xCOpEf/b7ni2o5m
bRLD83Wl1JZBcktW1mSU4A==
-----END CERTIFICATE-----]
certificate         -----BEGIN CERTIFICATE-----
MIIDZzCCAk+gAwIBAgIUImnKxB3erkVXMQMZ8dxp6Q1RjPowDQYJKoZIhvcNAQEL
BQAwLDEqMCgGA1UEAxMhZXhhbXBsZS5ydSBJbnRlcm1lZGlhdGUgQXV0aG9yaXR5
MB4XDTE5MTIyMjExNTYyMloXDTE5MTIyMzExNTY1MlowHDEaMBgGA1UEAxMRZ2l0
bGFiLmV4YW1wbGUucnUwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDH
ShdQ1h8jj4CEpzvqGNPLfNqOck4mMg2YWojTqtG8ZgUj/AA3Q+LUId7OP5U1JzOq
y8FdtalQUgsdio4kCZcmhDpjvuWP1oGf3RiIoiNrMl5t4Hp0icSjfF4F7k0mTYyz
Ca7Kq4pC1laPzTC5abNhX6G9IVTNY4Nt10b8mgnjQ9Ycr1/EJakUbnKo7V3Oi1sw
UfYnRURtdwfTCCcNUI3laTS+t6tQvcgkKkDYra11ex3/k8JlQaWzWtPn7aCV9OSQ
FNCnX7oX2ZxqlQvTjCVuhMWwgt1LS6m/RQ5ST/tLUen/f85Qgj+ciIk1t7oTmjG7
86ujGU9UgV9qtu4n24orAgMBAAGjgZAwgY0wDgYDVR0PAQH/BAQDAgOoMB0GA1Ud
JQQWMBQGCCsGAQUFBwMBBggrBgEFBQcDAjAdBgNVHQ4EFgQUFVgm+9Cj7CoGwWwh
yIOqTTQL/L4wHwYDVR0jBBgwFoAUHmUVD8OeN5rjt4jyJhDBQgWyOjQwHAYDVR0R
BBUwE4IRZ2l0bGFiLmV4YW1wbGUucnUwDQYJKoZIhvcNAQELBQADggEBAEe1Lep0
QNU180Kjjei8eMfsdIqAd0/YIUNXtDxlkyNPkABCyAzWr7jthh294lG6jIvLoSsg
yIXBcWjIuZr6LBt+GtThoTWUnscc9S3BKf/R0aFSFtUVAP5wC9lW8XP1E9IuPTfF
y7XRj6vDuHhffD2laj5EIK2niN71sgbgjAx3TLupBiYvsh/PrVUzzCO3LMGiDag3
MBpGMN0jYMDtexMqdL3BQ802iLC2D3ibLx5MfgHM7G+qOeZ5hE3cp+rxqYL2fRZA
f+nG62FcLxG/Pz3mFSBW9AGc8nBuTZJDIdv7bii2PaYmdHI+A5Fp28z6IPDLT84G
uubR7nR5/2NY6B8=
-----END CERTIFICATE-----
expiration          1577102212
issuing_ca          -----BEGIN CERTIFICATE-----
MIIDnDCCAoSgAwIBAgIUH5mwnTKCrFjBE7rykjDxaVjkG/owDQYJKoZIhvcNAQEL
BQAwFTETMBEGA1UEAxMKZXhtYXBsZS5ydTAeFw0xOTEyMjIxMTQyMDhaFw0yNDEy
MjAxMTQyMzhaMCwxKjAoBgNVBAMTIWV4YW1wbGUucnUgSW50ZXJtZWRpYXRlIEF1
dGhvcml0eTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBALHAP27OZevI
zt2fSy5W3vpVufbJu6+3daBsXoUxOaMWmkVm6ob1qoUxpm/WQLT+xhmndt7pv4pD
ACqUEKzHL2JTlXemvKAXBmXNYJWH60fcG6DgjtEh7ny/Og0pdXlKgEf4ItbQtMC5
dKz0G3YNaxj0OWdZWTGN1qDWQT3sECHtBv4YudPDwMCCJDeDR909AZSmDLay3C9s
OasXCO/R4PsST7JMkl4gjXktm20kaqMt95N/uYfh7Lkwcu95XrtA7F6EHWuiuCqT
EymTBaiUuskzsuNzh0KRe0THFlCNDL7W76SLAL+c8nmCszH7ylmOf/tnbKEJd5aQ
dBIPXBS9/RcCAwEAAaOBzDCByTAOBgNVHQ8BAf8EBAMCAQYwDwYDVR0TAQH/BAUw
AwEB/zAdBgNVHQ4EFgQUHmUVD8OeN5rjt4jyJhDBQgWyOjQwHwYDVR0jBBgwFoAU
TcOGNriRV5TqXnYybrgQbAk0JcYwNwYIKwYBBQUHAQEEKzApMCcGCCsGAQUFBzAC
hhtodHRwOi8vdmF1bHQ6ODIwMC92MS9wa2kvY2EwLQYDVR0fBCYwJDAioCCgHoYc
aHR0cDovL3ZhdWx0OjgyMDAvdjEvcGtpL2NybDANBgkqhkiG9w0BAQsFAAOCAQEA
nwVjpLZG+JMmlY4Kb9yQb8m1Gq8whiH8qu7AZ142rduAYtILtJ9bFRBl6bXBI/lX
WCUi+u8hG4UVDr76j6H22W9I+YHIsRWIunm+/SCLxWbXDPbtrIy8OX7XmfCAV5YO
nvLimMKwZbzzdTZvPhkGXCpGY+D8xLByJH5alFzlak7bJx8CXISijMjNP+1RM8xn
ERgch+c6IRYwHTFyNlruikrdHwVB/Of0TnlQlVFAQ5q1lciks4JQLbkCO/Yhnw31
d+mKPW6lFDd+gHa4WPmwfIRL8irw0vb3ugHjDtQd+h7toBTY4xCOpEf/b7ni2o5m
bRLD83Wl1JZBcktW1mSU4A==
-----END CERTIFICATE-----
private_key         -----BEGIN RSA PRIVATE KEY-----
MIIEpAIBAAKCAQEAx0oXUNYfI4+AhKc76hjTy3zajnJOJjINmFqI06rRvGYFI/wA
N0Pi1CHezj+VNSczqsvBXbWpUFILHYqOJAmXJoQ6Y77lj9aBn90YiKIjazJebeB6
dInEo3xeBe5NJk2MswmuyquKQtZWj80wuWmzYV+hvSFUzWODbddG/JoJ40PWHK9f
xCWpFG5yqO1dzotbMFH2J0VEbXcH0wgnDVCN5Wk0vrerUL3IJCpA2K2tdXsd/5PC
ZUGls1rT5+2glfTkkBTQp1+6F9mcapUL04wlboTFsILdS0upv0UOUk/7S1Hp/3/O
UII/nIiJNbe6E5oxu/OroxlPVIFfarbuJ9uKKwIDAQABAoIBAEMlOn/Y9PrlQbbw
Jaa1IL8B3R17c8s4uA3qCwnOFwZ0FS1Pnb8BF27Dzq8NCzeycp0MM6VgLKDMU8Rr
R4Lq3Et057ttuSjibCAxvS9j+a0Hdnts07VqKZ+vqnozFTlfjZVRINxWOapVTyr6
Rn1iGLcpInR3w3vEGKcsjlVTjRtNyqJfEPq01VOwwWa9H4U8hLYmZHMGH3wMDZHG
2LGSU+hB5afxYmqBpwLOeNgFvYGVzRKoWGPidX2bmZBwpblp2TnvZNjoqSdh1EF8
TCPeQz+yDRDmZRYmxxnv0ygCSXKvIMuqJYEgJKjZMyYCiKmM4wkEEnGf3hxadB4u
Pfh/aZECgYEA5EYOgdYylRdx6X7Jaycv5kHjl6X443kzgTQLqA3wLOb4gToiFjC9
YT74xGg/neqg9B4ZKnDgqT7BAjjNG5mZARaciMtaV+tr1CszrgSmtLLipb6r5qIB
H1++OjkPgmn4iyMVSIiD2iyL+Yk4Lprc9fv7AhrG0jw4CFO0GCE8pokCgYEA337M
U9n21YPykG2ZE2bWhjW1Ng++ROU+ZPGBWqtzGNzj8YLbO7FQ4RZbQx1C/uPdae43
KghDhbNeM9ABFkzb2ifa0WFRuiVYZWVvm5m/BH6ujlQjHGdq31LGRJ4Bg2JtKr/0
TMWfAYXyVexcIULPhXyfVtacV+xILOSk2x5WPhMCgYEAjqlx25cAWckjOegw/TSq
bN703AuwNonCtJbQSiEb3xQibrDkKLYhRStT0XvNdmXyXV6KH8oXve4oNblGBl/C
DboR7jQYrCXHZ/vv+muRvBCoMSmuyR6qUO9HNZ//n2OXH223mXMn5Cw+9p7Za9cB
DQKD5tbRNwbHhfy56vKqsvECgYASCx+d6Wv3IvbzWBXSrivFnBJsMesvjr5x0dma
o1rgd/zjI6hlclRQR8jIa1VcVLMseTH078PbyHZjgniD/2KEg1E0NEIv4BH55a2U
B16Xk06uaPaItYS4UOt24LbIj6q2l9a2WAIVUmYVL8bkXIt6gGwwy5TFuGq/TRpO
Im3eBwKBgQCjjIp508rsEOz5whFbsr6lJlEiehH1kICCvncsxqbijo8+Bs08LfWJ
GpXH7QI76oYv/PcmfT7Z6rDRJ+uJyP/gOXlrml3CAggkSmp+1EmXzUqhu4kiHqSY
9hRhF0LtL93SVYTW+BBmOzFRYLYVmOPsfpdQkJF0LDB90cZfQy77+w==
-----END RSA PRIVATE KEY-----
private_key_type    rsa
serial_number       22:69:ca:c4:1d:de:ae:45:57:31:03:19:f1:dc:69:e9:0d:51:8c:fa</code></pre>
