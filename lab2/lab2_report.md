University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4112c  
Author: Korchagina Daria 
Lab: Lab2  
Date of create: 16.10.2023  
Date of finished: 
---
## Развертывание веб сервиса в Minikube, доступ к веб интерфейсу сервиса. Мониторинг сервиса.

### Цель работы

Ознакомиться с типами "контроллеров" развертывания контейнеров, ознакомиться с сетевыми сервисами и развернуть свое веб приложение. 

### Ход работы
### 1. Создание `deployment` с 2 репликами контейнера [ifilyaninitmo/itdt-contained-frontend:master](https://hub.docker.com/repository/docker/ifilyaninitmo/itdt-contained-frontend) и передача переменных в эти реплики: `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME`.



![docker](/lab1/Docker.png)

### 2. Запуск Minikube
Развернут minikube cluster командой: 
```
minikube start
```
![start_minikube](/lab1/minikubestart.png)

### 3. Создание манифеста
Создан файл `vault-pod.yaml`, который описывает конфигурацю "пода". На скриншоте представлена конфигурация "пода".
Открываем текстовый редактор и создаем файл vault-pod.yaml:
```
nano vault-pod.yaml
```
Выводим его содержимое на экран:
```
cat vault-pod.yaml
```
![vault-pod](/lab1/cat.png)

### 4. Создание "пода" в кластере
Используем команду:
```
kubectl apply -f vault-pod.yaml
```
### 5. Cоздание сервиса для доступа к контейнеру
Для доступа к контейнеру создаем сервис с помощью команды:
```
minikube kubectl -- expose pod vault --type=NodePort --port=8200
```
### 6. Прокидываение порта
Для того, чтобы зайти в контейнер, прокинут порт локального компьютера в контейнер с помощью команды:
```
minikube kubectl -- port-forward service/vault 8200:8200
```
После этого переходим по ссылке [http://localhost:8200](http://localhost:8200), где открывается интерфейс.

![vault](/lab1/vault.png)

### 7. Получение токена для входа в Vault
Чтобы найти токен, открываем логи с помощью комнады:
```
kubectl logs vault
```
Результат выполнения команды:

![token](/lab1/Token.png)

Токен для входа располагается в поле `Root Token`. С помощью него можно авторизоваться в Vault, что показано на скрине.

![log_vault](/lab1/Login.png)
