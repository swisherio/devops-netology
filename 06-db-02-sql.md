## Домашнее задание к занятию "6.2. SQL"
### Введение
##### Перед выполнением задания вы можете ознакомиться с дополнительными материалами.

Задача 1
Используя docker поднимите инстанс PostgreSQL (версию 12) c 2 volume, в который будут складываться данные БД и бэкапы.
```
Ответ:
12:17:28 tretyakov@pc:~$ docker pull postgres:12
13:00:03 tretyakov@pc:~$ docker volume create vol2
vol2
13:00:15 tretyakov@pc:~$ docker volume create vol1
vol1
13:00:03 tretyakov@pc:~$ docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol1:/var/lib/postgresql/data -v vol2:/var/lib/postgresql postgres:12
13:00:35 tretyakov@pc:~$ docker run --rm --name pg-docker -e POSTGRES_PASSWORD=postgres -ti -p 5432:5432 -v vol1:/var/lib/postgresql/data -v vol2:/var/lib/postgresql postgres:12
```

Приведите получившуюся команду или docker-compose манифест.
![](https://github.com/swisherio/devops-netology/blob/main/ss_6_1.png)

Задача 2
В БД из задачи 1:
создайте пользователя test-admin-user и БД test_db
в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже)
предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db
создайте пользователя test-simple-user
предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE данных таблиц БД test_db

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
SQL-запрос для выдачи списка пользователей с правами над таблицами test_db
список пользователей с правами над таблицами test_db
```
CREATE DATABASE test_db
CREATE ROLE "test-admin-user" SUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;

CREATE TABLE orders 
(
id integer, 
name text, 
price integer, 
PRIMARY KEY (id) 
);

CREATE TABLE clients 
(
	id integer PRIMARY KEY,
	lastname text,
	country text,
	booking integer,
	FOREIGN KEY (booking) REFERENCES orders (Id)
);

CREATE ROLE "test-simple-user" NOSUPERUSER NOCREATEDB NOCREATEROLE NOINHERIT LOGIN;
GRANT SELECT ON TABLE public.clients TO "test-simple-user";
GRANT INSERT ON TABLE public.clients TO "test-simple-user";
GRANT UPDATE ON TABLE public.clients TO "test-simple-user";
GRANT DELETE ON TABLE public.clients TO "test-simple-user";
GRANT SELECT ON TABLE public.orders TO "test-simple-user";
GRANT INSERT ON TABLE public.orders TO "test-simple-user";
GRANT UPDATE ON TABLE public.orders TO "test-simple-user";
GRANT DELETE ON TABLE public.orders TO "test-simple-user";
```
![](https://github.com/swisherio/devops-netology/blob/main/ss_6_2_0.png)
![](https://github.com/swisherio/devops-netology/blob/main/ss_6_2_1.png)
![](https://)
Задача 3

Используя SQL синтаксис - наполните таблицы следующими тестовыми данными:

Таблица orders

|Наименование|цена|
|------------|----|
|Шоколад| 10 |
|Принтер| 3000 |
|Книга| 500 |
|Монитор| 7000|
|Гитара| 4000|

Таблица clients

|ФИО|Страна проживания|
|------------|----|
|Иванов Иван Иванович| USA |
|Петров Петр Петрович| Canada |
|Иоганн Себастьян Бах| Japan |
|Ронни Джеймс Дио| Russia|
|Ritchie Blackmore| Russia|

Используя SQL синтаксис:
- вычислите количество записей для каждой таблицы 
- приведите в ответе:
    - запросы 
    - результаты их выполнения.
```
insert into orders VALUES (1, 'Шоколад', 10), (2, 'Принтер', 3000), (3, 'Книга', 500), (4, 'Монитор', 7000), (5, 'Гитара', 4000);
insert into clients VALUES (1, 'Иванов Иван Иванович', 'USA'), (2, 'Петров Петр Петрович', 'Canada'), (3, 'Иоганн Себастьян Бах', 'Japan'), (4, 'Ронни Джеймс Дио', 'Russia'), (5, 'Ritchie Blackmore', 'Russia');
select count (*) from orders;
select count (*) from clients;
```
![](https://github.com/swisherio/devops-netology/blob/main/ss_6_3_1.png)
Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения данных операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод данного запроса.
 
Подсказк - используйте директиву `UPDATE`.
```
update  clients set booking = 3 where id = 1;
update  clients set booking = 4 where id = 2;
update  clients set booking = 5 where id = 3;

Запрос получения Вариант1 -  учитывает наличие в таблице заказов данных:
select * from clients as c where  exists (select id from orders as o where c.booking = o.id) ;
Запрос получения Вариант2 - проверяет на непустые значения у клиентов по заказам:
select * from clients where booking is not null
```
![](https://github.com/swisherio/devops-netology/blob/main/ss_6_4_1.png)
Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните что значат полученные значения.

```
Вариант 1
Показывает стоимость(нагрузку на исполнение) запроса (не оптимальный в сравнении с Вар2)
Показывает шаги связи, и сбор сканирование таблиц после связи

Вариант 2
Так же показывает стоимость(нагрузку на исполнение) запроса , и фильтрацию по полю Booking для выборки.
```
![](https://github.com/swisherio/devops-netology/blob/main/ss_6_5_1.png)

Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. Задачу 1).

Остановите контейнер с PostgreSQL (но не удаляйте volumes).

Поднимите новый пустой контейнер с PostgreSQL.

Восстановите БД test_db в новом контейнере.

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 
```

17:23:15 tretyakov@pc:~$ docker exec -t pgre-docker pg_dump -U postgres test_db -f /var/lib/postgresql/data/dump_test.sql

17:29:18 tretyakov@pc:~$ docker exec -i pgre-docker2 psql -U postgres -d test_db -f /var/lib/postgresql/data/dump_test.sql
SET
SET
SET
SET
SET
 set_config 
------------
 
(1 row)

SET
SET
SET
SET
SET
SET
CREATE TABLE
ALTER TABLE
CREATE TABLE
ALTER TABLE
COPY 5
COPY 5
ALTER TABLE
ALTER TABLE
ALTER TABLE
GRANT
GRANT
17:33:29 tretyakov@pc:~$ 
```
