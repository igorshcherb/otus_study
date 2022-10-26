## PostgreSQL для администраторов баз данных и разработчиков
## Занятие 02. SQL и реляционные СУБД. Введение в PostgreSQL 
# Домашнее задание

запустить везде psql из под пользователя postgres
выключить auto commit

**postgres=# \set AUTOCOMMIT off**
**postgres=# \set**
**. . .**
**AUTOCOMMIT = 'off'**
**. . .**

сделать в первой сессии новую таблицу и наполнить ее данными create table persons(id serial, first_name text, second_name text); 
insert into persons(first_name, second_name) values('ivan', 'ivanov'); 
insert into persons(first_name, second_name) values('petr', 'petrov'); commit;

**postgres=# create table persons(id serial, first_name text, second_name text);**

**CREATE TABLE**

**postgres=*# insert into persons(first_name, second_name) values('ivan', 'ivanov');**

**INSERT 0 1**

**postgres=*# insert into persons(first_name, second_name) values('petr', 'petrov');**

**INSERT 0 1**

**postgres=*# commit;**

**COMMIT**

посмотреть текущий уровень изоляции: show transaction isolation level

**postgres=*# show transaction isolation level;**

** transaction_isolation**

**-----------------------**

** read committed**

начать новую транзакцию в обоих сессиях с дефолтным (не меняя) уровнем изоляции
в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sergey', 'sergeev');

**postgres=# insert into persons(first_name, second_name) values('sergey', 'sergeev');**

**INSERT 0 1**

сделать select * from persons во второй сессии

**postgres=# select * from persons;**

** id | first_name | second_name**

**----+------------+-------------**

**  1 | ivan       | ivanov**

**  2 | petr       | petrov**

видите ли вы новую запись и если да то почему?

**Новая запись не видна во второй сессии, как как текущий уровень изоляции read committed, а в первой сессии еще не сделан commit после добавления записи.**

завершить первую транзакцию - commit;

**postgres=*# commit;**

**COMMIT**

сделать select * from persons во второй сессии

**postgres=*# select * from persons;**

** id | first_name | second_name**

**----+------------+-------------**

**  1 | ivan       | ivanov**

**  2 | petr       | petrov**

**  3 | sergey     | sergeev**

видите ли вы новую запись и если да то почему?

**Новая запись видна во второй сессии, так как уровень изоляции read committed, и в первой сессии был сделан commit.**

завершите транзакцию во второй сессии

**postgres=*# commit;**

**COMMIT**

начать новые но уже repeatable read транзации - set transaction isolation level repeatable read;

**postgres=# set transaction isolation level repeatable read;**

**SET**

в первой сессии добавить новую запись insert into persons(first_name, second_name) values('sveta', 'svetova');

**postgres=*# insert into persons(first_name, second_name) values('sveta', 'svetova');**

**INSERT 0 1**

сделать select * from persons во второй сессии

**postgres=*# select * from persons;**

** id | first_name | second_name**

**----+------------+-------------**

**  1 | ivan       | ivanov**

**  2 | petr       | petrov**

**  3 | sergey     | sergeev**


видите ли вы новую запись и если да то почему?

**Новая запись видна во второй сессии, так как уровень изоляции repeatable read, а новой записи еще не было на начало транзакции.**

завершить первую транзакцию - commit;

**postgres=*# commit;**

**COMMIT**

сделать select * from persons во второй сессии

**postgres=*# select * from persons;**

** id | first_name | second_name**

**----+------------+-------------**

**  1 | ivan       | ivanov**

**  2 | petr       | petrov**

**  3 | sergey     | sergeev**


видите ли вы новую запись и если да то почему?

**Новая запись не видна во второй сессии, так как уровень изоляции repeatable read, а новой записи еще не было на начало текущей транзакции.**

завершить вторую транзакцию

**postgres=*# commit;**

**COMMIT**

сделать select * from persons во второй сессии

**postgres=# select * from persons;

** id | first_name | second_name

**----+------------+-------------

**  1 | ivan       | ivanov

**  2 | petr       | petrov

**  3 | sergey     | sergeev

**  4 | sveta      | svetova


видите ли вы новую запись и если да то почему?

**Новая запись видна во второй сессии, так как уровень изоляции repeatable read, и новая запись была закомичена до начала текущей транзакции.**


