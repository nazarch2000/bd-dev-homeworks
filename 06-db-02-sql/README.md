# Домашнее задание к занятию 2. «SQL»

## Введение

Перед выполнением задания вы можете ознакомиться с 
[дополнительными материалами](https://github.com/netology-code/virt-homeworks/blob/virt-11/additional/README.md).

## Задача 1

Используя Docker, поднимите инстанс PostgreSQL (версию 12) c 2 volume, 
в который будут складываться данные БД и бэкапы.

Приведите получившуюся команду или docker-compose-манифест.

![image](https://github.com/nazarch2000/bd-dev-homeworks/assets/106932460/e38c6f41-5f4b-4957-adae-38d15be247f9)

## Задача 2

В БД из задачи 1: 

- создайте пользователя test-admin-user и БД test_db;
- в БД test_db создайте таблицу orders и clients (спeцификация таблиц ниже);
- предоставьте привилегии на все операции пользователю test-admin-user на таблицы БД test_db;
- создайте пользователя test-simple-user;
- предоставьте пользователю test-simple-user права на SELECT/INSERT/UPDATE/DELETE этих таблиц БД test_db.

Таблица orders:

- id (serial primary key);
- наименование (string);
- цена (integer).

Таблица clients:

- id (serial primary key);
- фамилия (string);
- страна проживания (string, index);
- заказ (foreign key orders).

Приведите:

- итоговый список БД после выполнения пунктов выше;
- описание таблиц (describe);
- SQL-запрос для выдачи списка пользователей с правами над таблицами test_db;
- список пользователей с правами над таблицами test_db.

![image](https://github.com/nazarch2000/bd-dev-homeworks/assets/106932460/5de02007-4aba-4bf7-a418-47e0ffa47439)

## Задача 3

Используя SQL-синтаксис, наполните таблицы следующими тестовыми данными:

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

Используя SQL-синтаксис:
- вычислите количество записей для каждой таблицы.

Приведите в ответе:

    - запросы,
    - результаты их выполнения.

![image](https://github.com/nazarch2000/bd-dev-homeworks/assets/106932460/9882efad-6a61-44b7-a890-ff26372503ef)
![image](https://github.com/nazarch2000/bd-dev-homeworks/assets/106932460/b98e368e-7178-423a-8737-6bc06b4b9fc4)
![image](https://github.com/nazarch2000/bd-dev-homeworks/assets/106932460/441d816b-bf93-4201-bb39-27eb6834f97c)

## Задача 4

Часть пользователей из таблицы clients решили оформить заказы из таблицы orders.

Используя foreign keys, свяжите записи из таблиц, согласно таблице:

|ФИО|Заказ|
|------------|----|
|Иванов Иван Иванович| Книга |
|Петров Петр Петрович| Монитор |
|Иоганн Себастьян Бах| Гитара |

Приведите SQL-запросы для выполнения этих операций.

Приведите SQL-запрос для выдачи всех пользователей, которые совершили заказ, а также вывод этого запроса.
 
Подсказка: используйте директиву `UPDATE`.
![image](https://github.com/nazarch2000/bd-dev-homeworks/assets/106932460/9723730d-e707-4e29-bb57-c92f0fb5e3c6)

## Задача 5

Получите полную информацию по выполнению запроса выдачи всех пользователей из задачи 4 
(используя директиву EXPLAIN).

Приведите получившийся результат и объясните, что значат полученные значения.

```
Hash Join  (cost=37.00..57.24 rows=810 width=68) (actual time=0.023..0.026 rows=6 loops=1) 
  Hash Cond: (c.id_order = o.id_order) # Связь между столбцами id_order в таблицах clients orders
  ->  Seq Scan on clients c  (cost=0.00..18.10 rows=810 width=36) (actual time=0.008..0.009 rows=10 loops=1)
# Операция последовательного сканирования
  ->  Hash  (cost=22.00..22.00 rows=1200 width=36) (actual time=0.008..0.009 rows=5 loops=1) # Операция построения хеш-таблицы
        Buckets: 2048  Batches: 1  Memory Usage: 17kB
        ->  Seq Scan on orders o  (cost=0.00..22.00 rows=1200 width=36) (actual time=0.004..0.005 rows=5 loops=1)
# Операция последовательного сканирования
Planning Time: 0.118 ms
Execution Time: 0.045 ms
```
## Задача 6

Создайте бэкап БД test_db и поместите его в volume, предназначенный для бэкапов (см. задачу 1).
```bash
docker exec 85532b1b0ae3 pg_dump -U test-admin-user -d test_db -Fc -f /var/lib/postgresql/backups/backup_file.dump
docker cp postgres_db_1:/var/lib/postgresql/backups/backup.sql ./postgres_backups/backup.sql
```
Остановите контейнер с PostgreSQL, но не удаляйте volumes.
```
docker stop postgres_db_1
```
Поднимите новый пустой контейнер с PostgreSQL.
```
docker run --name postges_db_2 -e POSTGRES_PASSWORD=123 -v /root/postgres/postgres_backups/:/var/lib/postgresql/backups -d postgres:12
```
Восстановите БД test_db в новом контейнере.
```
docker exec postges_db_2 psql -U postgres -c "CREATE DATABASE test_db;"
docker exec postges_db_2 pg_restore -U postgres -d test_db /var/lib/postgresql/backups/backup_file.dump
```

Приведите список операций, который вы применяли для бэкапа данных и восстановления. 
---

### Как cдавать задание

Выполненное домашнее задание пришлите ссылкой на .md-файл в вашем репозитории.

---

