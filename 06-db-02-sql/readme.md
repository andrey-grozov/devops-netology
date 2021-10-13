## Домашнее задание к занятию "6.2. SQL"
Введение

Перед выполнением задания вы можете ознакомиться с дополнительными материалами.
#### Задача 1

Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose манифест.

    docker run --name postgres -e POSTGRES_PASSWORD=postgres -it --rm -v db-data:/var/lib/postgresql/data -v db-backup:/backup -p 5432:5432 postgres:12

#### Задача 2

В БД из задачи 1:

создайте пользователя test-admin-user и БД test_db

в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)

предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db

создайте пользователя test-simple-user

Таблица orders:

    id (serial primary key)
    наименование (string)
    цена (integer)

Таблица clients:

    id (serial primary key)
    фамилия (string)
    страна проживания (string, index)
    заказ (foreign key orders)

Приведите:
итоговый список БД после выполнения пунктов выше,
описание таблиц (describe)

     test_db-# \d order
                      Table "public.order"
        Column    |  Type   | Collation | Nullable | Default
    --------------+---------+-----------+----------+---------
     id           | integer |           | not null |
     наименование | text    |           |          |
     цена         | integer |           |          |
    Indexes:
        "id" PRIMARY KEY, btree (id)
    Referenced by:
        TABLE "clients" CONSTRAINT "заказ_fk" FOREIGN KEY ("заказ") REFERENCES "order"(id)
    
    test_db-# \d clients
                        Table "public.clients"
          Column       |  Type   | Collation | Nullable | Default
    -------------------+---------+-----------+----------+---------
     id                | integer |           | not null |
     фамилия           | text    |           |          |
     страна проживания | text    |           |          |
     заказ             | integer |           |          |
    Indexes:
        "idcl" PRIMARY KEY, btree (id)
        "clients_фамилия_idx" btree ("фамилия")
    Foreign-key constraints:
        "заказ_fk" FOREIGN KEY ("заказ") REFERENCES "order"(id)

SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
    
    SELECT * FROM information_schema.table_privileges where table_name = 'order' or table_name = 'clients'

список пользователей с правами над таблицами test_db

    grantor |grantee         |table_catalog|table_schema|table_name|privilege_type|is_grantable|with_hierarchy|
    --------+----------------+-------------+------------+----------+--------------+------------+--------------+
    postgres|postgres        |test_db      |public      |order     |INSERT        |YES         |NO            |
    postgres|postgres        |test_db      |public      |order     |SELECT        |YES         |YES           |
    postgres|postgres        |test_db      |public      |order     |UPDATE        |YES         |NO            |
    postgres|postgres        |test_db      |public      |order     |DELETE        |YES         |NO            |
    postgres|postgres        |test_db      |public      |order     |TRUNCATE      |YES         |NO            |
    postgres|postgres        |test_db      |public      |order     |REFERENCES    |YES         |NO            |
    postgres|postgres        |test_db      |public      |order     |TRIGGER       |YES         |NO            |
    postgres|test-admin-user |test_db      |public      |order     |INSERT        |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |order     |SELECT        |NO          |YES           |
    postgres|test-admin-user |test_db      |public      |order     |UPDATE        |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |order     |DELETE        |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |order     |TRUNCATE      |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |order     |REFERENCES    |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |order     |TRIGGER       |NO          |NO            |
    postgres|test-simple-user|test_db      |public      |order     |INSERT        |NO          |NO            |
    postgres|test-simple-user|test_db      |public      |order     |SELECT        |NO          |YES           |
    postgres|test-simple-user|test_db      |public      |order     |UPDATE        |NO          |NO            |
    postgres|test-simple-user|test_db      |public      |order     |DELETE        |NO          |NO            |
    postgres|postgres        |test_db      |public      |clients   |INSERT        |YES         |NO            |
    postgres|postgres        |test_db      |public      |clients   |SELECT        |YES         |YES           |
    postgres|postgres        |test_db      |public      |clients   |UPDATE        |YES         |NO            |
    postgres|postgres        |test_db      |public      |clients   |DELETE        |YES         |NO            |
    postgres|postgres        |test_db      |public      |clients   |TRUNCATE      |YES         |NO            |
    postgres|postgres        |test_db      |public      |clients   |REFERENCES    |YES         |NO            |
    postgres|postgres        |test_db      |public      |clients   |TRIGGER       |YES         |NO            |
    postgres|test-admin-user |test_db      |public      |clients   |INSERT        |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |clients   |SELECT        |NO          |YES           |
    postgres|test-admin-user |test_db      |public      |clients   |UPDATE        |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |clients   |DELETE        |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |clients   |TRUNCATE      |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |clients   |REFERENCES    |NO          |NO            |
    postgres|test-admin-user |test_db      |public      |clients   |TRIGGER       |NO          |NO            |
    postgres|test-simple-user|test_db      |public      |clients   |INSERT        |NO          |NO            |
    postgres|test-simple-user|test_db      |public      |clients   |SELECT        |NO          |YES           |
    postgres|test-simple-user|test_db      |public      |clients   |UPDATE        |NO          |NO            |
    postgres|test-simple-user|test_db      |public      |clients   |DELETE        |NO          |NO            |

#### Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

    Таблица orders
    Наименование 	цена
    Шоколад 	10
    Принтер 	3000
    Книга 	500
    Монитор 	7000
    Гитара 	4000
    
    Таблица clients
    ФИО 	Страна проживания
    Иванов Иван Иванович 	USA
    Петров Петр Петрович 	Canada
    Иоганн Себастьян Бах 	Japan
    Ронни Джеймс Дио 	Russia
    Ritchie Blackmore 	Russia

Используя SQL синтаксис:

вычислите количество записей для каждой таблицы

    приведите в ответе:
        запросы
        результаты их выполнения.

    select count(*) from order          - 5 
    select count(*) from clients        - 5


#### Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

    Используя foreign keys свяжите записи из таблиц, согласно таблице:
    ФИО 	Заказ
    Иванов Иван Иванович 	Книга
    Петров Петр Петрович 	Монитор
    Иоганн Себастьян Бах 	Гитара

Приведите SQL-запросы для выполнения данных операций.

    ALTER TABLE clients ADD CONSTRAINT clients_fk FOREIGN KEY (заказ) REFERENCES "order"(id);
    UPDATE clients SET заказ = 3 WHERE id = 1;
    UPDATE clients SET заказ = 2 WHERE id = 4;
    UPDATE clients SET заказ = 3 WHERE id = 5;

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.

    select фамилия,заказ from clients where заказ >0
    Иванов Иван Иванович	3
    Петров Петр Петрович	4
    Иоганн Себастьян Бах	5
  
#### Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 (используя директиву EXPLAIN).
    
    explain select фамилия,заказ from clients where заказ >0

Приведите получившийся результат и объясните что значат полученные значения.
    
    QUERY PLAN                                            |
    ------------------------------------------------------+
    Seq Scan on clients  (cost=0.00..1.06 rows=3 width=37)|
    Filter: ("заказ" > 0)                               |    
    Это означает последовательное, блок за блоком, чтение данных таблицы.
    cost - некое понятия. призванное оченить затратность операции. 
    rows — приблизительное количество возвращаемых строк при выполнении операции Seq Scan. Это значение возвращает планировщик.
    width — средний размер одной строки в байтах.

#### Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

Остановите контейнер с PostgreSQL (но не удаляйте volumes).

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления.
    
    pg_dump -f /backup/test_db.dump -Upostgres test_db -сохраняем БД на примонтированый volume и останавливаем контейнер
    
    Запускаем контейнер с новымми volume
    docker run --name postgres -e POSTGRES_PASSWORD=postgres -it --rm -v newdb-data:/var/lib/postgresql/data -v db-backup:/backup -p 5432:5432 postgres:12
    Восстановление БД
    create database test_db OWNER 'postgres' ENCODING 'UTF8' - создаем БД
    psql -Upostgres test_db < /backup/test_db.dump - восстанавливаем БД из примонтированного volume
    

