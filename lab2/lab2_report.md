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

Создаем yaml файл деплоймента при помощи команды:

```
nano deployment.yaml
```
Содержимое файла:

![deployment](/lab2/Deployment.png)

### 2. Создание сервиса, через который будет доступ на "поды".
В том же yaml файле создаем сервис:

![service](/lab2/Service.png)

Запускаем:

![apply](/lab2/Apply.png)

### 3. Запуск в `minikube` режима проброса портов и подключение к контейнерам через веб браузер.

Прокидываем порт локального компьютера, чтобы подключиться к контейнерам через браузер:

![port-forward](/lab2/PF.png)

### 4. Проверка переменных `REACT_APP_USERNAME`, `REACT_APP_COMPANY_NAME` и `Container name`.

Открываем в браузере [http://localhost:3000](http://localhost:3000):

![3000](/lab2/3000.png)

При переходе по ссылке запрос попадает на одну из двух реплик контенейров, которые мы создали. На странице мы можем видеть название контенера, на который попал запрос, а также его IP внутри кластера. Эти значения могут измениьтся, если запрос попадет на второй контенейр. Значения переменнных  `REACT_APP_USERNAME` и `REACT_APP_COMPANY_NAME`меняться не будут.


### 5. Проверка логов контейнеров.
Для того, чтобы узнать названия контенейров, можно воспользоваться командой, которая выведет информацию обо всем содержимом кластера:

```
 minikube kubectl -- get all
```
![get all](/lab2/GA.png)
Зная названия контейнеров, мы можем посмотреть логи каждого из них:
![Logs](/lab2/Logs.png)

## Схема организации контейнеров и сервисов

