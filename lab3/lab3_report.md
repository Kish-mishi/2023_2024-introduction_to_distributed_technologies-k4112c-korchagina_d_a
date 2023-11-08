University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4112c  
Author: Korchagina Daria 
Lab: Lab3 
Date of create: 06.11.2023  
Date of finished: 
-------
## Лабораторная работа №3 "Сертификаты и "секреты" в Minikube, безопасное хранение данных."

### Цель работы
Познакомиться с сертификатами и "секретами" в Minikube, правилами безопасного хранения данных в Minikube. 

### Ход работы

### 1. Создание манифеста

Создаем манифест командой:

```
nano lab3.yaml
```
В манифесте `lab3`создаются ConfigMap, Deployment с ReplicaSet и сервис для доступа к подам. 
В ConfigMap переменные`react_app_user_name` и `react_app_company_name`задаются как значения `Darisha` и `ITMO`.
Манифест:

```
apiVersion: v1
kind: ConfigMap
metadata:
  name: config-lab3
data:
  REACT_APP_USERNAME: "Darisha"
  REACT_APP_COMPANY_NAME: "ITMO"

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app-lab3
spec:
  replicas: 2
  selector:
    matchLabels:
      app: app-lab3
  template:
    metadata:
      labels:
        app: app-lab3
    spec:
      containers:
      - name: app-lab3
        image: ifilyaninitmo/itdt-contained-frontend:master
        envFrom:
          - configMapRef:
              name: config-lab3
        ports:
          - containerPort: 3000

---
apiVersion: v1
kind: Service
metadata:
  name: service-lab3
spec:
  selector:
    app: app-lab3
  ports:
    - port: 80
      protocol: TCP
      name: http
  type: NodePort
```
Применяем манифест командой:

```
kubectl apply -f lab3.yaml
```
### 2. Создание сертификата

```
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout tls.key -out tls.crt -subj "/CN=lab3.info"
```
### 3. Создание секрета

```
kubectl create secret tls tls-secret --cert=tls.crt --key=tls.key
```
### 4. Создание ingress

Подключение ingress:

```
minikube addons enable ingress
```
Создание ingress:

```
nano ingress.yaml
```
Содержимое:
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-lab3
spec:
  rules:
  - host: lab3.info
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: service-lab3
            port:
              number: 80
  tls:
  - hosts:
    - lab3.info
    secretName: tls-secret
```
### 5. Доступ к приложению

Узнаем IP: 

```
minikube ip
```
Вписываем его и доменное имя в hosts:

```
sudo nano /etc/hosts
```
Перенаправляем трафик: 

```
minikube tunnel
```
Переходим в сервис по доменному имени [https://lab3.info](https://lab3.info)


