University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4112c  
Author: Korchagina Daria 
Lab: Lab4  
Date of create: 06.11.2023  
Date of finished: 
---
## Лабораторная работа №4 "Сети связи в Minikube, CNI и CoreDNS"
## Цель работы
Познакомиться с CNI Calico и функцией `IPAM Plugin`, изучить особенности работы CNI и CoreDNS.
### Ход работы
## 1. Запуск minikube
Запускаем minikube с подключенным плагином `CNI=calico`, режимом работы `Multi-Node Clusters` и разворачиваем 2 ноды при помощью команды:

```
minikube start --network-plugin=cni --cni=calico --nodes 2 -p multinode-demo
```
![](/lab4/pictures/minikube_start.png)

Проверка количества нод:
```
kubectl get nodes
```
![](/lab4/pictures/get_nodes.png)

Проверка количества подов calico(их должно быть столько же, сколько нод):
```
kubectl get pods -l k8s-app=calico-node -A
```
![](/lab4/pictures/3.png)

## 2. Помечаем ноды по географическому положению

Назначаем метки узлам:

```
kubectl label nodes multinode-demo zone=east  
kubectl label nodes multinode-demo-m02 zone=west
```
Устанавливаем calicoctl, для того, чтобы создать манифест для IPPool:

```
kubectl create -f calicoctl.yaml
```
![](/lab4/pictires/4.png)

До создания IPPools, необходимо удалить созданные по-умолчанию:

```
kubectl exec -i -n kube-system calicoctl -- /calicoctl --allow-version-mismatch get ippools -o wide
kubectl delete ippools default-ipv4-ippool
```
![](/lab4/pictures/5.png)

Создаем IPPools:

```
kubectl exec -i -n kube-system calicoctl -- /calicoctl --allow-version-mismatch create -f - < ippool.yaml
```
![](/lab4/pictures/create_ippool.png)

Проверяем:

```
kubectl exec -i -n kube-system calicoctl -- /calicoctl --allow-version-mismatch get ippool -o wide
```
![](/lab4/pictures/created.png)

##3.  Создаем деплоймент и сервис

```
kubectl apply -f deployment.yaml -f service.yaml
```
![](/lab4/pictures/apply_deploy.png)

Проверка:

```
kubectl get deployments
```

```
kubectl get services
```
![](/lab4/pictures/get_services.png)

Смотрим IP подов:

```
kubectl get pods -o wide
```
![](/lab4/pictures/check_ip.png)

### Прокидывание порта

```
kubectl port-forward service/service 8200:3000
```
По ссылке `http://localhost:8200/` видим:

![](/lab4/pictures/localhost.png)

Container name и Container IP могут меняться, в зависимости от того, на какой под  попал запрос.

### Пингуем поды
Заходим в контейнер `deployment-68c469d755-hxwl2`:

```
kubectl exec -ti deployment-68c469d755-hxwl2 -- sh
```
И пингуем контейнер с IP  192.168.1.192:

![](/lab4/pictures/ping.png)

Наоборот, заходим в контейнер `deployment-68c469d755-jrc26`:

```
kubectl exec -ti ddeployment-68c469d755-jrc26 -- sh
```
И пингуем контейнер с IP  192.168.0.65:

![](/lab4/pictures/ping2.png)

### Схема
![](/lab4/pictures/scheme.png)
