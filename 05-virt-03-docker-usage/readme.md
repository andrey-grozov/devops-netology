## Домашнее задание к занятию "5.3. Контейнеризация на примере Docker"

#### Задача 1

Посмотрите на сценарий ниже и ответьте на вопрос: "Подходит ли в этом сценарии использование докера? Или лучше подойдет виртуальная машина, физическая машина? Или возможны разные варианты?"

Детально опишите и обоснуйте свой выбор.

--

Сценарий:

    Высоконагруженное монолитное java веб-приложение;           - виртуальная машина (нет необходимости создавать контейнеры, одно прилождение)
    Go-микросервис для генерации отчетов;                       - докер (много однотипных приложений)
    Nodejs веб-приложение;                                      - докер (много однотипных приложений)    
    Мобильное приложение c версиями для Android и iOS;          - докер (много однотипных приложений) 
    База данных postgresql используемая, как кэш;               - физическая машина (лучшее быстродействие с БД)
    Шина данных на базе Apache Kafka;                           - докер ( возможность запуска на различных узлах в сети) 
    Очередь для Logstash на базе Redis;                         - 
    Elastic stack для реализации логирования продуктивного веб-приложения - три ноды elasticsearch, два logstash и две ноды kibana; - виртуальные машины + докер (запуск нескольких приложений в рамках одной виртуальной машины)
    Мониторинг-стек на базе prometheus и grafana;               - виртуальная машина виртуальная машина (нет необходимости создавать контейнеры)
    Mongodb, как основное хранилище данных для java-приложения; - физическая машина ( лучшее быстродействие с БД)
    Jenkins-сервер.                                             - докер ( возможность запуска множества однотипных задач) 

#### Задача 2

Сценарий выполения задачи:

    создайте свой репозиторий на докерхаб;
    выберете любой образ, который содержит апачи веб-сервер;
    создайте свой форк образа;
    реализуйте функциональность: запуск веб-сервера в фоне с индекс-страницей, содержащей HTML-код ниже:

<html>
<head>
Hey, Netology
</head>
<body>
<h1>I’m kinda DevOps now</h1>
</body>
</html>

Опубликуйте созданный форк в своем репозитории и предоставьте ответ в виде ссылки на докерхаб-репо.
    
    Ссылка на репозиторий в докерхабе https://hub.docker.com/repository/docker/mrgrav/netology

#### Задача 3

    Запустите первый контейнер из образа centos c любым тэгом в фоновом режиме, подключив папку info из текущей рабочей директории на хостовой машине в /share/info контейнера;
        root@vagrant:/home# docker run --name centos-01 -v /home/vagrant/info:/share/info -d centos sleep 99999
        d8b5571ddc371fe3e1a0ef8abb2c48cc0830da1841daa345242da2e59e12a350
    Запустите второй контейнер из образа debian:latest в фоновом режиме, подключив папку info из текущей рабочей директории на хостовой машине в /info контейнера;
        root@vagrant:/home# docker run --name debian-01 -v /home/vagrant/info:/info -d debian sleep 99999
        f79d86a91855a8288d336fa19c71ecb08ddb0159f452d91aa413b5218fad6d63
    Подключитесь к первому контейнеру с помощью exec и создайте текстовый файл любого содержания в /share/info ;
        root@vagrant:/home# docker exec -ti centos-01 bash
        [root@d8b5571ddc37 /]# echo "test file" > /share/info/file.test
    Добавьте еще один файл в папку info на хостовой машине;
        root@vagrant:/home# echo "test file 1" > /home/vagrant/info/file1.test
    Подключитесь во второй контейнер и отобразите листинг и содержание файлов в /info контейнера.
        root@vagrant:/home# docker exec -ti debian-01 bash
        root@f79d86a91855:/# ls -la /info
        total 16
        drwxr-xr-x 2 root root 4096 Sep  7 09:27 .
        drwxr-xr-x 1 root root 4096 Sep  7 09:25 ..
        -rw-r--r-- 1 root root   10 Sep  7 09:26 file.test
        -rw-r--r-- 1 root root   12 Sep  7 09:27 file1.test
        