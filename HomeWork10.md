## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 10. Блокировки. ##  
# Домашнее задание # 

>1. Настройте сервер так, чтобы в журнал сообщений сбрасывалась информация о блокировках, удерживаемых более 200 миллисекунд.  

В файле /etc/postgresql/14/main/postgresql.conf  
**log_lock_waits = on**  
**log_min_duration_statement = 200**  
sudo pg_ctlcluster 14 main stop  
sudo pg_ctlcluster 14 main start  

psql -U postgres -c 'show log_lock_waits'  
 log_lock_waits   
**----------------**  
 on  
(1 row)  

psql -U postgres -c 'show log_min_duration_statement'  
 log_min_duration_statement   
**----------------------------**  
 200ms  
(1 row)  
 
>Воспроизведите ситуацию, при которой в журнале появятся такие сообщения.  

**В первой сессии:**  
postgres=# \set AUTOCOMMIT off  
create table test1(vc varchar(50));  
insert into test1(vc) values ('01');  
insert into test1(vc) values ('02');  
update test1 set vc = vc || 'x';  

**Во второй сессии:**  
update test1 set vc = vc || 'x';  

**Журнал:**  
pg_lsclusters  
Ver Cluster Port Status Owner    Data directory              Log file  
14  main    5432 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log  

**В журнале:**  
2022-11-23 18:08:19.161 MSK [4095] postgres@postgres LOG:  process 4095 still waiting for ShareLock on transaction 558446 after 1001.131 ms  
2022-11-23 18:08:19.161 MSK [4095] postgres@postgres DETAIL:  Process holding the lock: 4046. Wait queue: 4095.  
2022-11-23 18:08:19.161 MSK [4095] postgres@postgres CONTEXT:  while updating tuple (0,3) in relation "test1"  
2022-11-23 18:08:19.161 MSK [4095] postgres@postgres STATEMENT:  update test1 set vc = vc || 'x';  
2022-11-23 18:08:58.272 MSK [4095] postgres@postgres LOG:  process 4095 acquired ShareLock on transaction 558446 after 40112.591 ms  
2022-11-23 18:08:58.272 MSK [4095] postgres@postgres CONTEXT:  while updating tuple (0,3) in relation "test1"  
2022-11-23 18:08:58.272 MSK [4095] postgres@postgres STATEMENT:  update test1 set vc = vc || 'x';  
2022-11-23 18:08:58.272 MSK [4095] postgres@postgres LOG:  duration: 40113.133 ms  statement: update test1 set vc = vc || 'x';  

>2. Смоделируйте ситуацию обновления одной и той же строки тремя командами UPDATE в разных сеансах.   
>Изучите возникшие блокировки в представлении pg_locks и убедитесь, что все они понятны.   
>Пришлите список блокировок и объясните, что значит каждая.  

**В первой сессии:**  
create table test2(id numeric, vc varchar(50));  
insert into test2(id, vc) values (1, 'one');  
\set AUTOCOMMIT off  
update test2 set vc = vc || '**' where id = 1;  

**Вo второй сессии:**  
\set AUTOCOMMIT off  
update test2 set vc = vc || '**' where id = 1;  

**В третьей сессии:**  
\set AUTOCOMMIT off  
update test2 set vc = vc || '**' where id = 1;  

**В четвертой сессии:**  
select * from pg_locks \gx  

-[ RECORD 1 ]------+------------------------------  
locktype           | relation  
database           | 13799  
relation           | 12290  
page               |   
tuple              |   
virtualxid         |   
transactionid      |   
virtualtransaction | 6/8  
pid                | 2380  
mode               | AccessShareLock  
granted            | t  
fastpath           | t  
waitstart          |  
-[ RECORD 2 ]------+------------------------------  
locktype           | virtualxid  
database           |   
relation           |   
page               |   
tuple              |   
virtualxid         | 6/8  
transactionid      |   
virtualtransaction | 6/8  
pid                | 2380  
mode               | ExclusiveLock  
granted            | t  
fastpath           | t  
waitstart          |   
-[ RECORD 3 ]------+------------------------------  
locktype           | relation  
database           | 13799  
relation           | 16410  
page               |   
tuple              |   
virtualxid         |   
transactionid      |   
virtualtransaction | 5/2  
pid                | 2146  
mode               | RowExclusiveLock  
granted            | t  
fastpath           | t  
waitstart          |   
-[ RECORD 4 ]------+------------------------------  
locktype           | virtualxid  
database           |   
relation           |   
page               |   
tuple              |   
virtualxid         | 5/2  
transactionid      |   
virtualtransaction | 5/2  
pid                | 2146  
mode               | ExclusiveLock  
granted            | t  
fastpath           | t  
waitstart          |   
[ RECORD 5 ]------+------------------------------  
locktype           | relation  
database           | 13799  
relation           | 16410  
page               |   
tuple              |   
virtualxid         |   
transactionid      |   
virtualtransaction | 4/5  
pid                | 2139  
mode               | RowExclusiveLock  
granted            | t  
fastpath           | t  
waitstart          |   
-[ RECORD 6 ]------+------------------------------  
locktype           | virtualxid  
database           |   
relation           |   
page               |   
tuple              |   
virtualxid         | 4/5  
transactionid      |   
virtualtransaction | 4/5  
pid                | 2139  
mode               | ExclusiveLock  
granted            | t  
fastpath           | t  
waitstart          |   
-[ RECORD 7 ]------+------------------------------  
locktype           | relation   
database           | 13799  
relation           | 16410  
page               |   
tuple              |   
virtualxid         |   
transactionid      |   
virtualtransaction | 3/23  
pid                | 2136  
mode               | RowExclusiveLock  
granted            | t  
fastpath           | t  
waitstart          |   
-[ RECORD 8 ]------+------------------------------  
locktype           | virtualxid  
database           |   
relation           |   
page               |   
tuple              |   
virtualxid         | 3/23  
transactionid      |   
virtualtransaction | 3/23  
pid                | 2136  
mode               | ExclusiveLock  
granted            | t  
fastpath           | t  
waitstart          |   
[ RECORD 9 ]------+------------------------------  
locktype           | transactionid  
database           |   
relation           |   
page               |   
tuple              |   
virtualxid         |   
transactionid      | 558454  
virtualtransaction | 3/23  
pid                | 2136  
mode               | ExclusiveLock  
granted            | t  
fastpath           | f  
waitstart          |   
[ RECORD 10 ]-----+------------------------------  
locktype           | tuple  
database           | 13799  
relation           | 16410  
page               | 0  
tuple              | 6  
virtualxid         |   
transactionid      |   
virtualtransaction | 5/2  
pid                | 2146  
mode               | ExclusiveLock  
granted            | f  
fastpath           | f  
waitstart          | 2022-11-23 18:37:15.31474+03  
[ RECORD 11 ]-----+------------------------------  
locktype           | transactionid  
database           |   
relation           |   
page               |   
tuple              |   
virtualxid         |   
transactionid      | 558455  
virtualtransaction | 4/5  
pid                | 2139  
mode               | ExclusiveLock  
granted            | t  
fastpath           | f  
waitstart          |   
-[ RECORD 12 ]-----+------------------------------  
locktype           | transactionid  
database           |   
relation           |   
page               |   
tuple              |   
virtualxid         |   
transactionid      | 558456  
virtualtransaction | 5/2  
pid                | 2146  
mode               | ExclusiveLock  
granted            | t  
fastpath           | f  
waitstart          |   
-[ RECORD 13 ]-----+------------------------------  
locktype           | transactionid  
database           |   
relation           |   
page               |   
tuple              |   
virtualxid         |   
transactionid      | 558454  
virtualtransaction | 4/5  
pid                | 2139  
mode               | ShareLock  
granted            | f  
fastpath           | f  
waitstart          | 2022-11-23 18:37:10.802256+03  
-[ RECORD 14 ]-----+------------------------------  
locktype           | tuple  
database           | 13799  
relation           | 16410  
page               | 0  
tuple              | 6  
virtualxid         |   
transactionid      |   
virtualtransaction | 4/5  
pid                | 2139  
mode               | ExclusiveLock  
granted            | t  
fastpath           | f  
waitstart          |   

select * from pg_class where oid = 12290 \gx  
-[ RECORD 1 ]-------+----------------------------------------  
oid                 | 12290  
relname             | pg_locks  

select * from pg_class where oid = 16410 \gx  
-[ RECORD 1 ]-------+-------  
oid                 | 16410  
relname             | test2  


**Выводы:**  
Процесс 2380 заблокировал представление pg_locks (12290) в режиме AccessShareLock.  
3 процесса: 2146, 2139, 2136 - заблокировали таблицу test2 (16410) в режиме RowExclusiveLock.  
2139 заблокировал строку в test2 (16410) в режиме ExclusiveLock.  
2146, 2136 пытаются заблокировать строку в test2, но пока не могут и ожидают.  
Для каждой незавершенной транзакции есть строка transactionid.  
virtualxid - виртуальные идентификаторы. 

>3. Воспроизведите взаимоблокировку трех транзакций.   

**В первой сессии:**  
insert into test2(id, vc) values (2, 'two');  
insert into test2(id, vc) values (3, 'three');  
\set AUTOCOMMIT off  
update test2 set vc = vc || '&&' where id = 1;  

**Во второй сессии:**  
\set AUTOCOMMIT off  
update test2 set vc = vc || '&&' where id = 2;  

**В третьей сессии:**  
\set AUTOCOMMIT off  
update test2 set vc = vc || '&&' where id = 3;  

**В первой сессии:**  
update test2 set vc = vc || '&&' where id = 2;  

**Во второй сессии:**  
update test2 set vc = vc || '&&' where id = 3;  

**В третьей сессии:**  
update test2 set vc = vc || '&&' where id = 1;  

ERROR:  deadlock detected  
DETAIL:  Process 2074 waits for ShareLock on transaction 558461; blocked by process 2070.  
Process 2070 waits for ShareLock on transaction 558462; blocked by process 2072.  
Process 2072 waits for ShareLock on transaction 558463; blocked by process 2074.  
HINT:  See server log for query details.  
CONTEXT:  while updating tuple (0,9) in relation "test2"  

**В журнале сообщений:**  

2022-11-23 19:46:35.300 MSK [2074] postgres@postgres ERROR:  deadlock detected  
2022-11-23 19:46:35.300 MSK [2074] postgres@postgres DETAIL:  Process 2074 waits for ShareLock on transaction 558461; blocked by process 2070.  
	Process 2070 waits for ShareLock on transaction 558462; blocked by process 2072.  
	Process 2072 waits for ShareLock on transaction 558463; blocked by process 2074.  
	Process 2074: update test2 set vc = vc || '&&' where id = 1;  
	Process 2070: update test2 set vc = vc || '&&' where id = 2;  
	Process 2072: update test2 set vc = vc || '&&' where id = 3;  
2022-11-23 19:46:35.300 MSK [2074] postgres@postgres HINT:  See server log for query details.  
2022-11-23 19:46:35.300 MSK [2074] postgres@postgres CONTEXT:  while updating tuple (0,9) in relation "test2"  
2022-11-23 19:46:35.300 MSK [2074] postgres@postgres STATEMENT:  update test2 set vc = vc || '&&' where id = 1;  
2022-11-23 19:46:35.301 MSK [2072] postgres@postgres LOG:  process 2072 acquired ShareLock on transaction 558463 after 13561.726 ms  
2022-11-23 19:46:35.301 MSK [2072] postgres@postgres CONTEXT:  while updating tuple (0,11) in relation "test2"  
2022-11-23 19:46:35.301 MSK [2072] postgres@postgres STATEMENT:  update test2 set vc = vc || '&&' where id = 3;  
2022-11-23 19:46:35.301 MSK [2072] postgres@postgres LOG:  duration: 13562.029 ms  statement: update test2 set vc = vc || '&&' where id = 3;  

>Можно ли разобраться в ситуации постфактум, изучая журнал сообщений?

**Да, вполне можно разобраться.**  

>4. Могут ли две транзакции, выполняющие единственную команду UPDATE одной и той же таблицы (без where), заблокировать друг друга?  

**Нет, две транзакции не могут одновременно владеть блокировками конфликтующих режимов для одной и той же таблицы.**  

