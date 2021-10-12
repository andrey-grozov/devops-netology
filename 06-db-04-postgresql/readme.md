## [Домашнее задание к занятию "6.4. PostgreSQL"](/06-db-04-postgresql/readme.md)

#### Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 13). Данные БД сохраните в volume.

Подключитесь к БД PostgreSQL используя psql.

Воспользуйтесь командой \? для вывода подсказки по имеющимся в psql управляющим командам.

Найдите и приведите управляющие команды для:

    вывода списка БД                    postgres=# \l
    подключения к БД                    postgres=# \c
    вывода списка таблиц                postgres-# \dt
    вывода описания содержимого таблиц  postgres-# \d[S+]
    выхода из psql                      postgres-# \q

#### Задача 2

Используя psql создайте БД test_database.

    root@d746fe79ed48:/# psql -Upostgres -c "CREATE DATABASE test_database"

Изучите бэкап БД.

Восстановите бэкап БД в test_database.

    root@0f7bf5a9c00d:/# psql -Upostgres test_database < /rest/test_dump.sql

Перейдите в управляющую консоль psql внутри контейнера.

Подключитесь к восстановленной БД и проведите операцию ANALYZE для сбора статистики по таблице.

Используя таблицу pg_stats, найдите столбец таблицы orders с наибольшим средним значением размера элементов в байтах.

Приведите в ответе команду, которую вы использовали для вычисления и полученный результат.

    select * from pg_stats where tablename = 'orders'
    schemaname|tablename|attname|inherited|null_frac|avg_width|n_distinct|most_common_vals|most_common_freqs|histogram_bounds                                                                                                                                 |correlation|most_common_elems|most_common_elem_freqs|elem_count_histogram|
    ----------+---------+-------+---------+---------+---------+----------+----------------+-----------------+-------------------------------------------------------------------------------------------------------------------------------------------------+-----------+-----------------+----------------------+--------------------+
    public    |orders   |id     |false    |      0.0|        4|      -1.0|                |NULL             |{1,2,3,4,5,6,7,8}                                                                                                                                |        1.0|                 |NULL                  |NULL                |
    public    |orders   |title  |false    |      0.0|       16|      -1.0|                |NULL             |{"Adventure psql time",Dbiezdmin,"Log gossips","Me and my bash-pet","My little database","Server gravity falls","WAL never lies","War and peace"}| -0.3809524|                 |NULL                  |NULL                |
    public    |orders   |price  |false    |      0.0|        4|    -0.875|{300}           |{0.25}           |{100,123,499,500,501,900}                                                                                                                        |  0.5952381|                 |NULL                  |NULL                |
    
    Столбец с наибольшим значением размера элементов в байтах - avg_width



#### Задача 3

Архитектор и администратор БД выяснили, что ваша таблица orders разрослась до невиданных размеров и поиск по ней занимает долгое время. Вам, как успешному выпускнику курсов DevOps в нетологии предложили провести разбиение таблицы на 2 (шардировать на orders_1 - price>499 и orders_2 - price<=499).

Предложите SQL-транзакцию для проведения данной операции.
    
    create table orders_1 (
        check (price > 499)
    ) inherits (orders);
    
    create table orders_2 (
        check (price <= 499)
    ) inherits (orders);

Можно ли было изначально исключить "ручное" разбиение при проектировании таблицы orders?

    Да можно если при изначальном проектировании таблиц сделать ее секционированной.

#### Задача 4

Используя утилиту pg_dump создайте бекап БД test_database.
    
    root@0f7bf5a9c00d:/# pg_dump -U postgres test_database > /rest/test_database.dump
    
Как бы вы доработали бэкап-файл, чтобы добавить уникальность значения столбца title для таблиц test_database?

    Необходимо добавить строку
    ALTER TABLE ONLY public.orders
    ADD CONSTRAINT orders_title_key UNIQUE (title);

