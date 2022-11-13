## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 07. Логический уровень PostgreSQL ##  
# Домашнее задание #  

>1 создайте новый кластер PostgresSQL 14
**$ sudo -i -u postgres**
**$ /usr/lib/postgresql/14/bin/initdb -D /var/lib/postgresql/14/notmain**

**/var/lib/postgresql/14/notmain/postgresql.conf**
**port = 5433**

**/usr/lib/postgresql/14/bin/pg_ctl start -D /var/lib/postgresql/14/notmain**

**waiting for server to start....2022-11-10 08:49:38.179 MSK [3849] LOG:  starting PostgreSQL 14.5 (Ubuntu 14.5-0ubuntu0.22.04.1) on x86_64-pc-linux-gnu, compiled by gcc (Ubuntu 11.2.0-19ubuntu1) 11.2.0, 64-bit**
**2022-11-10 20:49:38.179 MSK [3849] LOG:  listening on IPv4 address "127.0.0.1", port 5433**
**2022-11-10 20:49:38.181 MSK [3849] LOG:  listening on Unix socket "/var/run/postgresql/.s.PGSQL.5433"**
**2022-11-10 20:49:38.188 MSK [3850] LOG:  database system was shut down at 2022-11-07 19:40:04 MSK**
**2022-11-10 20:49:38.194 MSK [3849] LOG:  database system is ready to accept connections**
**done**
**server started**

**psql -U postgres -p 5433 -c 'SELECT now();'**
**now**           
**-------------------------------**
**2022-11-10 20:54:10.769898+03**
**(1 row)**


>2 зайдите в созданный кластер под пользователем postgres

**psql -U postgres -p 5433**

>3 создайте новую базу данных testdb

**create database testdb;**

>4 зайдите в созданную базу данных под пользователем postgres

**psql -p 5433 -d testdb -U postgres**

>5 создайте новую схему testnm

**testdb=# create schema testnm;**
**CREATE SCHEMA**

>6 создайте новую таблицу t1 с одной колонкой c1 типа integer

**testdb=# set schema 'testnm';**
**SET**

**testdb=# create table t1 (c1 integer);**
**CREATE TABLE**

>7 вставьте строку со значением c1=1

**insert into t1(c1) values (1);**

>8 создайте новую роль readonly

**testdb=# create role readonly;**
**CREATE ROLE**

>9 дайте новой роли право на подключение к базе данных testdb

**testdb=# grant connect on database testdb to readonly;**
**GRANT**

>10 дайте новой роли право на использование схемы testnm

**testdb=# grant usage on schema testnm to readonly;**
**GRANT

>11 дайте новой роли право на select для всех таблиц схемы testnm

**testdb=# grant select on all tables in schema testnm to readonly;**
**GRANT**

>12 создайте пользователя testread с паролем test123

**testdb=# create user testread with password 'test123';**
**CREATE ROLE**

>13 дайте роль readonly пользователю testread

**testdb=# grant readonly to testread;**
**GRANT ROLE**

>14 зайдите под пользователем testread в базу данных testdb

**testdb=# \c - testread**
**You are now connected to database "testdb" as user "testread".**

>15 сделайте select * from t1;

**testdb=> select * from testnm.t1;**
**c1**
**----**
**1**
**(1 row)**

>16 получилось? (могло если вы делали сами не по шпаргалке и не упустили один существенный момент про который позже)

**Получилось, потому что указал схему (set schema).**

**Еще можно указывать схему в качестве префикса к именам таблиц,**

**использовать set search_path to testnm;**

**использовать alter user testread set search_path to testnm;**

>17 напишите что именно произошло в тексте домашнего задания
>18 у вас есть идеи почему? ведь права то дали?

**Если бы не указал схему:**

**select * from t1;**

**2022-11-10 21:42:40.674 MSK [4162] ERROR:  relation "t1" does not exist at character 15**

**2022-11-10 21:42:40.674 MSK [4162] STATEMENT:  select * from t1;**

**ERROR:  relation "t1" does not exist**

**LINE 1: select * from t1;**

**postgres=# show search_path;**

**search_path**

**-----------------**

**"$user", public**

**(1 row)**


>19 посмотрите на список таблиц

**testdb=> \dt testnm.* **
**List of relations**
**Schema | Name | Type  |  Owner** 
**--------+------+-------+----------**
**testnm | t1   | table | postgres**
**(1 row)**

>20 подсказка в шпаргалке под пунктом 20
>21 а почему так получилось с таблицей (если делали сами и без шпаргалки то может у вас все нормально)

**У меня все нормально.**

>22 вернитесь в базу данных testdb под пользователем postgres

**testdb=> \c - postgres**
**You are now connected to database "testdb" as user "postgres".**

>23 удалите таблицу t1

**testdb=# drop table testnm.t1;**
**DROP TABLE**

>24 создайте ее заново но уже с явным указанием имени схемы testnm

**testdb=# create table testnm.t1 (c1 integer);**
**CREATE TABLE**

>25 вставьте строку со значением c1=1

**testdb=# insert into testnm.t1(c1) values (1);**
**INSERT 0 1**

>26 зайдите под пользователем testread в базу данных testdb

**testdb=# \c - testread**
**You are now connected to database "testdb" as user "testread".**

>27 сделайте select * from testnm.t1;

**testdb=> select * from testnm.t1;**
**2022-11-10 22:00:02.098 MSK [4269] ERROR:  permission denied for table t1**
**2022-11-10 22:00:02.098 MSK [4269] STATEMENT:  select * from testnm.t1;**
**ERROR:  permission denied for table t1**

>28 получилось?

**Нет.**

>29 есть идеи почему? 

**"grant select on all tables in schema testnm to readonly;" дает привилегии только на существующие таблицы.**

>30 как сделать так чтобы такое больше не повторялось? 

**\c testdb postgres;**
**alter default privileges in schema testnm grant select on tables to readonly;**

>31 сделайте select * from testnm.t1;
>32 получилось?

**Нет.**

>33 есть идеи почему? если нет - смотрите шпаргалку

**"alter default" действует для новых таблиц.**

>31 сделайте select * from testnm.t1;

**select * from testnm.t1;**
**c1**
**----**
**1**
**(1 row)**

>32 получилось?
>33 ура!
>34 теперь попробуйте выполнить команду create table t2(c1 integer); insert into t2 values (2);
>35 а как так? нам же никто прав на создание таблиц и insert в них под ролью readonly?
>36 есть идеи как убрать эти права?

**\c testdb postgres;** 
**revoke CREATE on SCHEMA public FROM public;**
**revoke all on DATABASE testdb FROM public;**

>37 если вы справились сами то расскажите что сделали и почему, если смотрели шпаргалку - объясните что сделали и почему выполнив указанные в ней команды
>38 теперь попробуйте выполнить команду create table t3(c1 integer); insert into t2 values (2);
>39 расскажите что получилось и почему

**На этот раз команда создания таблицы под пользователем testread не выполнилась.**
