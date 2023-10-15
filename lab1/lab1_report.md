University: [ITMO University](https://itmo.ru/ru/)  
Faculty: [FICT](https://fict.itmo.ru)  
Course: [Introduction to distributed technologies](https://github.com/itmo-ict-faculty/introduction-to-distributed-technologies)  
Year: 2023/2024  
Group: K4112c  
Author: Korchagina Daria
Lab: Lab1  
Date of create: 15.10.2023  
Date of finished: 

---
# Установка Docker и Minikube, мой первый манифест
## Цель работы
Ознакомиться с инструментами Minikube и Docker, развернуть свой первый "под".
## Ход работы
### 1. Установка Docker
Установлен Docker Desktop и WLS.

![docker](/lab1/Docker.png)

### 2. Запуск Minikube
Развернут minikube cluster командой: 
```
minikube start
```
![start_minikube](/lab1/minikube start.png)

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

## Ответы на вопросы

1. `kubectl apply -f vault-pod.yaml` создает или обновляет ресурсы (объекты) в кластере на основе конфигурационного файла. Флаг `-f` указывает имя файла или URL-адрес файла, который содержит описание ресурсов.
2. `minikube kubectl -- expose pod vault --type=NodePort --port=8200` создает службу из указанного объекта с заданным именем. Флаг `--type=NodePort` указывает тип службы. Флаг `--port=8200` указывает порт, который открыт для доступа к создаваемой службе.
3. `minikube kubectl -- port-forward service/vault 8200:8200` настраивает прямое соединение между локальной машиной и ресуорсом внутри кластера. Агумент команды `service/vault` указывает на установление прямого соединения с сервисом Vault внутри кластера. Аргумент `8200:8200`определяет порты для прокидывания.

## Схема организации контейеров и сервисов

![scheme](/lab1/screenshots/scheme.jpg)
