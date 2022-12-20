## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 14. Виды и устройство репликации в PostgreSQL. Практика применения. ##  
# Домашнее задание # 

>На 1 ВМ создаем таблицы test для записи, test2 для запросов на чтение.  
>Создаем публикацию таблицы test и подписываемся на публикацию таблицы test2 с ВМ №2.  
>На 2 ВМ создаем таблицы test2 для записи, test для запросов на чтение.  
>Создаем публикацию таблицы test2 и подписываемся на публикацию таблицы test1 с ВМ №1.  
>3 ВМ использовать как реплику для чтения и бэкапов (подписаться на таблицы из ВМ №1 и №2 ).  
>Небольшое описание, того, что получилось.  

Преподаватель разрешил вместо отдельных ВМ использовать кластеры в одной ВМ.  

### Создание 2-го кластера ###

sudo -i -u postgres  
pg_createcluster -d /var/lib/postgresql/14/main2 14 main2  
sudo systemctl start postgresql@14-main2  

pg_lsclusters  
Ver Cluster Port Status Owner    Data directory               Log file  
14  main    5432 online postgres /var/lib/postgresql/14/main  /var/log/postgresql/postgresql-14-main.log  
14  main2   5433 online postgres /var/lib/postgresql/14/main2 /var/log/postgresql/postgresql-14-main2.log  

### Создание таблиц и публикаций ###

psql -U postgres -p 5432  

alter system set wal_level=logical;  

create table test(id int);  
create table test2(id int);  

\q  

sudo systemctl restart postgresql@14-main  

psql -U postgres -p 5432  

create publication test_pub for table test;  

\dRp+  
                           Publication test_pub  
  Owner   | All tables | Inserts | Updates | Deletes | Truncates | Via root   
----------+------------+---------+---------+---------+-----------+----------  
 postgres | f          | t       | t       | t       | t         | f  
Tables:  
    "public.test"  

\q  

psql -U postgres -p 5433  

alter system set wal_level=logical;  

create table test(id int);  
create table test2(id int);  
\q
  
sudo systemctl restart postgresql@14-main2  
psql -U postgres -p 5433  
create publication test2_pub for table test2;  

\dRp+  
                           Publication test2_pub  
  Owner   | All tables | Inserts | Updates | Deletes | Truncates | Via root   
----------+------------+---------+---------+---------+-----------+----------  
 postgres | f          | t       | t       | t       | t         | f  
Tables:  
    "public.test2"  


### Создание подписок ###

psql -U postgres -p 5432  
create subscription test2_sub  
connection 'host=localhost port=5433 user=postgres  
password=p dbname=postgres'  
publication test2_pub with (copy_data=true);  

\dRs  
            List of subscriptions  
   Name    |  Owner   | Enabled | Publication   
-----------+----------+---------+-------------  
 test2_sub | postgres | t       | {test2_pub}  

\q  
psql -U postgres -p 5433  
create subscription test_sub  
connection 'host=localhost port=5432 user=postgres  
password=p dbname=postgres'  
publication test_pub with (copy_data=true);  

\dRs  
            List of subscriptions  
   Name   |  Owner   | Enabled | Publication   
----------+----------+---------+-------------  
 test_sub | postgres | t       | {test_pub}  

\q  

### Проверка логической репликации ###

psql -U postgres -p 5432  
insert into test values (1), (2), (3);  
\q  
psql -U postgres -p 5433  
select * from test;  
 id   
**----**  
  1  
  2  
  3  
(3 rows)  

insert into test2 values (10), (20), (30);  
\q  

psql -U postgres -p 5432  
select * from test2;  
 id   
**----**  
 10  
 20  
 30  
(3 rows)  

### Создание 3-го кластера и подписок в нем ###  

sudo -i -u postgres    
pg_createcluster -d /var/lib/postgresql/14/main3 14 main3   
sudo systemctl start postgresql@14-main3  

pg_lsclusters  
Ver Cluster Port Status Owner    Data directory               Log file  
14  main    5432 online postgres /var/lib/postgresql/14/main  /var/log/postgresql/postgresql-14-main.log  
14  main2   5433 online postgres /var/lib/postgresql/14/main2 /var/log/postgresql/postgresql-14-main2.log  
14  main3   5434 online postgres /var/lib/postgresql/14/main3 /var/log/postgresql/postgresql-14-main3.log  

psql -U postgres -p 5434  

create table test(id int);    
create table test2(id int);  

create subscription test2_sub3   
connection 'host=localhost port=5433 user=postgres   
password=p dbname=postgres'   
publication test2_pub with (copy_data=true);  

create subscription test_sub3   
connection 'host=localhost port=5432 user=postgres  
password=p dbname=postgres'  
publication test_pub with (copy_data=true);  

\dRs   
             List of subscriptions  
    Name    |  Owner   | Enabled | Publication  
------------+----------+---------+-------------  
 test2_sub3 | postgres | t       | {test2_pub}  
 test_sub3  | postgres | t       | {test_pub}  
(2 rows)  

select * from test;  
 id  
**----**   
  1  
  2  
  3  
(3 rows)  

select * from test2;  
 id  
**----**  
 10  
 20  
 30  
(3 rows)  



