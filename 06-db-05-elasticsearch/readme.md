## Домашнее задание к занятию "6.5. Elasticsearch"
#### Задача 1

В этом задании вы потренируетесь в:

    установке elasticsearch
    первоначальном конфигурировании elastcisearch
    запуске elasticsearch в docker

Используя докер образ centos:7 как базовый и документацию по установке и запуску Elastcisearch:

    составьте Dockerfile-манифест для elasticsearch
    соберите docker-образ и сделайте push в ваш docker.io репозиторий
    запустите контейнер из получившегося образа и выполните запрос пути / c хост-машины

Требования к elasticsearch.yml:

    данные path должны сохраняться в /var/lib
    имя ноды должно быть netology_test

В ответе приведите:

текст Dockerfile манифеста
    
    FROM centos:latest
    ENV PATH=/usr/lib:/usr/lib/jvm/jre-11/bin:$PATH
    EXPOSE 9200
    
    RUN yum install java-11-openjdk-devel -y 
    RUN yum install wget -y 
    
    RUN wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.0-linux-x86_64.tar.gz \
        && wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.15.0-linux-x86_64.tar.gz.sha512 
    RUN yum install perl-Digest-SHA -y 
    RUN shasum -a 512 -c elasticsearch-7.15.0-linux-x86_64.tar.gz.sha512 \ 
        && tar -xzf elasticsearch-7.15.0-linux-x86_64.tar.gz \
        && yum upgrade -y
        
    ADD elasticsearch.yml /elasticsearch-7.15.0/config/
    ENV ES_JAVA_HOME=/elasticsearch-7.15.0/jdk/
    ENV ES_HOME=/elasticsearch-7.15.0
    RUN groupadd elasticsearch \
        && useradd -g elasticsearch elasticsearch
        
    RUN mkdir /var/lib/logs \
        && chown elasticsearch:elasticsearch /var/lib/logs \
        && mkdir /var/lib/data \
        && chown elasticsearch:elasticsearch /var/lib/data \
        && chown -R elasticsearch:elasticsearch /elasticsearch-7.15.0/

    RUN mkdir /elasticsearch-7.15.0/snapshots &&\
    chown elasticsearch:elasticsearch /elasticsearch-7.15.0/snapshots
        
    USER elasticsearch
    CMD ["/elasticsearch-7.15.0/bin/elasticsearch"]

ссылку на образ в репозитории dockerhub

        https://hub.docker.com/layers/171361969/mrgrav/netology/elastic/images/sha256-b733fd9214a9e940912ef764bbe3de7292ad845685489b34028f7932c85f630d?context=repo

ответ elasticsearch на запрос пути / в json виде

    [elasticsearch@4f6267147424 /]$ curl -X GET 'http://localhost:9200/'
    {
      "name" : "4f6267147424",
      "cluster_name" : "netology_test",
      "cluster_uuid" : "XT3MT_2SSsy7K5yiVqHmHw",
      "version" : {
        "number" : "7.15.0",
        "build_flavor" : "default",
        "build_type" : "tar",
        "build_hash" : "79d65f6e357953a5b3cbcc5e2c7c21073d89aa29",
        "build_date" : "2021-09-16T03:05:29.143308416Z",
        "build_snapshot" : false,
        "lucene_version" : "8.9.0",
        "minimum_wire_compatibility_version" : "6.8.0",
        "minimum_index_compatibility_version" : "6.0.0-beta1"
      },
      "tagline" : "You Know, for Search"
    }

Подсказки:

    возможно вам понадобится установка пакета perl-Digest-SHA для корректной работы пакета shasum
    при сетевых проблемах внимательно изучите кластерные и сетевые настройки в elasticsearch.yml
    при некоторых проблемах вам поможет docker директива ulimit
    elasticsearch в логах обычно описывает проблему и пути ее решения

Далее мы будем работать с данным экземпляром elasticsearch.
#### Задача 2

В этом задании вы научитесь:

    создавать и удалять индексы
    изучать состояние кластера
    обосновывать причину деградации доступности данных

Ознакомтесь с документацией и добавьте в elasticsearch 3 индекса, в соответствии со таблицей:

    Имя 	Количество реплик 	Количество шард
    ind-1 	0 	1
    ind-2 	1 	2
    ind-3 	2 	4
    Создаем индексы:
    curl -X PUT localhost:9200/ind-1 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
    curl -X PUT localhost:9200/ind-2 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 2,  "number_of_replicas": 1 }}'
    curl -X PUT localhost:9200/ind-3 -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 4,  "number_of_replicas": 2 }}'

Получите список индексов и их статусов, используя API и приведите в ответе на задание.
    
Список индексов 

    $ curl -X GET 'http://localhost:9200/_cat/indices?v'
    health status index uuid                   pri rep docs.count docs.deleted store.size pri.store.size
    green  open   ind-1 Ogg6rYGJRTO_2Gk3fcXNkQ   1   0          0            0       208b           208b
    yellow open   ind-3 RwkuaeOyTHaYWVzOS2UGCQ   4   2          0            0       832b           832b
    yellow open   ind-2 TGk2kpy1S1yJ4wk88J6R0g   2   1          0            0       416b           416b

Статус индексов

    curl -X GET 'http://localhost:9200/_cluster/health/ind-1?pretty'
    {
      "cluster_name" : "netology_test",
      "status" : "green",
      "timed_out" : false,
      "number_of_nodes" : 1,
      "number_of_data_nodes" : 1,
      "active_primary_shards" : 1,
      "active_shards" : 1,
      "relocating_shards" : 0,
      "initializing_shards" : 0,
      "unassigned_shards" : 0,
      "delayed_unassigned_shards" : 0,
      "number_of_pending_tasks" : 0,
      "number_of_in_flight_fetch" : 0,
      "task_max_waiting_in_queue_millis" : 0,
      "active_shards_percent_as_number" : 100.0
    }
    curl -X GET 'http://localhost:9200/_cluster/health/ind-2?pretty'
    {
      "cluster_name" : "netology_test",
      "status" : "yellow",
      "timed_out" : false,
      "number_of_nodes" : 1,
      "number_of_data_nodes" : 1,
      "active_primary_shards" : 2,
      "active_shards" : 2,
      "relocating_shards" : 0,
      "initializing_shards" : 0,
      "unassigned_shards" : 2,
      "delayed_unassigned_shards" : 0,
      "number_of_pending_tasks" : 0,
      "number_of_in_flight_fetch" : 0,
      "task_max_waiting_in_queue_millis" : 0,
      "active_shards_percent_as_number" : 41.17647058823529
    }
    curl -X GET 'http://localhost:9200/_cluster/health/ind-3?pretty'
    {
      "cluster_name" : "netology_test",
      "status" : "yellow",
      "timed_out" : false,
      "number_of_nodes" : 1,
      "number_of_data_nodes" : 1,
      "active_primary_shards" : 4,
      "active_shards" : 4,
      "relocating_shards" : 0,
      "initializing_shards" : 0,
      "unassigned_shards" : 8,
      "delayed_unassigned_shards" : 0,
      "number_of_pending_tasks" : 0,
      "number_of_in_flight_fetch" : 0,
      "task_max_waiting_in_queue_millis" : 0,
      "active_shards_percent_as_number" : 41.17647058823529
    }    
Получите состояние кластера elasticsearch, используя API.

    curl -X GET localhost:9200/_cluster/health/?pretty=true
    {
      "cluster_name" : "netology_test",
      "status" : "yellow",
      "timed_out" : false,
      "number_of_nodes" : 1,
      "number_of_data_nodes" : 1,
      "active_primary_shards" : 7,
      "active_shards" : 7,
      "relocating_shards" : 0,
      "initializing_shards" : 0,
      "unassigned_shards" : 10,
      "delayed_unassigned_shards" : 0,
      "number_of_pending_tasks" : 0,
      "number_of_in_flight_fetch" : 0,
      "task_max_waiting_in_queue_millis" : 0,
      "active_shards_percent_as_number" : 41.17647058823529
    }

Как вы думаете, почему часть индексов и кластер находится в состоянии yellow?

    Индексы находятся в статусе Yellow потому что у них указано число реплик, а по факту руплицировать некуда, т.к. нет других серверов. 

Удалите все индексы.

    curl -X DELETE 'http://localhost:9200/ind-1?pretty'
    {
      "acknowledged" : true
    }
    $ curl -X DELETE 'http://localhost:9200/ind-2?pretty'
    {
      "acknowledged" : true
    }
    $ curl -X DELETE 'http://localhost:9200/ind-3?pretty'
    {
      "acknowledged" : true
    }

Важно

При проектировании кластера elasticsearch нужно корректно рассчитывать количество реплик и шард, иначе возможна потеря данных индексов, вплоть до полной, при деградации системы.

#### Задача 3

В данном задании вы научитесь:

создавать бэкапы данных

восстанавливать индексы из бэкапов

Создайте директорию {путь до корневой директории с elasticsearch в образе}/snapshots.

Используя API зарегистрируйте данную директорию как snapshot repository c именем netology_backup.
    
Приведите в ответе запрос API и результат вызова API для создания репозитория.

    curl -XPOST localhost:9200/_snapshot/netology_backup?pretty -H 'Content-Type: application/json' -d'{"type": "fs", "settings": { "location":"myrepo" }}'
    {
      "acknowledged" : true
    }
    curl -X GET 'http://localhost:9200/_snapshot/netology_backup?pretty'
    {
      "netology_backup" : {
        "type" : "fs",
        "settings" : {
          "location" : "myrepo"
        }
      }
    }

Создайте индекс test с 0 реплик и 1 шардом и приведите в ответе список индексов.

    curl -X PUT localhost:9200/test -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
    {"acknowledged":true,"shards_acknowledged":true,"index":"test"}
    curl -X GET 'http://localhost:9200/test?pretty'
    {
      "test" : {
        "aliases" : { },
        "mappings" : { },
        "settings" : {
          "index" : {
            "routing" : {
              "allocation" : {
                "include" : {
                  "_tier_preference" : "data_content"
                }
              }
            },
            "number_of_shards" : "1",
            "provided_name" : "test",
            "creation_date" : "1633919640686",
            "number_of_replicas" : "0",
            "uuid" : "6GCB3xOhRD-RlDjA225Gvw",
            "version" : {
              "created" : "7150099"
            }
          }
        }
      }
    }

Создайте snapshot состояния кластера elasticsearch.

    curl -X PUT localhost:9200/_snapshot/netology_backup/elasticsearch?wait_for_completion=true

Приведите в ответе список файлов в директории со snapshotами.

    [elasticsearch@06c066de7bf9 myrepo]$ pwd
    /elasticsearch-7.15.0/snapshots/myrepo
    [elasticsearch@06c066de7bf9 myrepo]$ ls -la
    total 52
    drwxr-xr-x 3 elasticsearch elasticsearch  4096 Oct 11 03:40 .
    drwxr-xr-x 1 elasticsearch elasticsearch  4096 Oct 11 03:40 ..
    -rw-r--r-- 1 elasticsearch elasticsearch   831 Oct 11 03:40 index-0
    -rw-r--r-- 1 elasticsearch elasticsearch     8 Oct 11 03:40 index.latest
    drwxr-xr-x 4 elasticsearch elasticsearch  4096 Oct 11 03:40 indices
    -rw-r--r-- 1 elasticsearch elasticsearch 27644 Oct 11 03:40 meta-igfRdYRHTuiHBGMSGMoxDA.dat
    -rw-r--r-- 1 elasticsearch elasticsearch   440 Oct 11 03:40 snap-igfRdYRHTuiHBGMSGMoxDA.dat

Удалите индекс test и создайте индекс test-2. Приведите в ответе список индексов.

    $ curl -X DELETE 'http://localhost:9200/test?pretty'
    {
      "acknowledged" : true
    }
    curl -X PUT localhost:9200/test-2?pretty -H 'Content-Type: application/json' -d'{ "settings": { "number_of_shards": 1,  "number_of_replicas": 0 }}'
    {
      "acknowledged" : true,
      "shards_acknowledged" : true,
      "index" : "test-2"
    }    

Восстановите состояние кластера elasticsearch из snapshot, созданного ранее.

Приведите в ответе запрос к API восстановления и итоговый список индексов.
    
    curl -X POST localhost:9200/_snapshot/netology_backup/elasticsearch/_restore?pretty -H 'Content-Type: application/json' -d'{"include_global_state":true}'
    {
      "accepted" : true
    }
    [elasticsearch@06c066de7bf9 myrepo]$ curl -X GET http://localhost:9200/_cat/indices?v
    health status index            uuid                   pri rep docs.count docs.deleted store.size pri.store.size
    green  open   .geoip_databases HelAzI4-SHKSo-AqURDOvQ   1   0         41            0     40.1mb         40.1mb
    green  open   test-2           6iHTxXRORru89AZQI2yAYw   1   0          0            0       208b           208b
    green  open   test             GyhfCQBUQ4GvpCWuZkl5GA   1   0          0            0       208b           208b

