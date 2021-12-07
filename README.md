# Задача 1: Работа с картами конфигураций через утилиту kubectl в установленном minikube

создал карты конфигураций:
```shell
root@control1:/home/desema# kubectl create configmap nginx-config --from-file=nginx.conf
configmap/nginx-config created

root@control1:/home/desema# kubectl create configmap domain --from-literal=name=netology.ru
configmap/domain created
```
далее просмотрел список имеющихся конфиг-мап:
```shell
root@control1:/home/desema# kubectl get configmap
NAME               DATA   AGE
domain             1      13s
kube-root-ca.crt   1      50d
nginx-config       1      19s

root@control1:/home/desema# kubectl get configmap nginx-config
NAME           DATA   AGE
nginx-config   1      39s

root@control1:/home/desema# kubectl describe configmap domain
Name:         domain
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
name:
----
netology.ru

BinaryData
====

Events:  <none>
```
потом посмотрел созданные мапы в разных форматах:
```shell
root@control1:/home/desema# kubectl get configmap nginx-config -o yaml
apiVersion: v1
data:
  nginx.conf: |
    server {
        listen 80;
        server_name  netology.ru www.netology.ru;
        access_log  /var/log/nginx/domains/netology.ru-access.log  main;
        error_log   /var/log/nginx/domains/netology.ru-error.log info;
        location / {
            include proxy_params;
            proxy_pass http://10.10.10.10:8080/;
        }
    }
kind: ConfigMap
metadata:
  creationTimestamp: "2021-12-07T13:35:22Z"
  name: nginx-config
  namespace: default
  resourceVersion: "479446"
  uid: 8f3fd3a4-196b-40e8-a489-6a205ca1efa6
  
root@control1:/home/desema# kubectl get configmap domain -o json
{
    "apiVersion": "v1",
    "data": {
        "name": "netology.ru"
    },
    "kind": "ConfigMap",
    "metadata": {
        "creationTimestamp": "2021-12-07T13:35:28Z",
        "name": "domain",
        "namespace": "default",
        "resourceVersion": "479459",
        "uid": "bd91889b-17ba-4005-b46d-8340912d7287"
    }
}
```
после экспортировал конфиг-мапу в файл и пересоздал из него же:
```shell
root@control1:/home/desema# kubectl get configmap nginx-config -o yaml > nginx-config.yml

root@control1:/home/desema# kubectl delete configmap nginx-config
configmap "nginx-config" deleted

root@control1:/home/desema# kubectl apply -f nginx-config.yml
configmap/nginx-config created

root@control1:/home/desema# kubectl get configmap
NAME               DATA   AGE
domain             1      11m
kube-root-ca.crt   1      50d
nginx-config       1      8s
```
