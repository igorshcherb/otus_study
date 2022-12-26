## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 20. Секционирование. ##  
# Домашнее задание # 

>Секционировать большую таблицу из демо базы flights.  

### Скачал БД ### 
https://www.postgrespro.ru/education/demodb  
Переименовал файл в demo.sql  

### Установил БД ### 
sudo -i -u postgres  
psql  
\i /tmp/demo.sql  

### Определил самую большую таблицу этой БД ### 
Для определения размера таблиц использовал DBeaver.  
Самая большая таблица - ticket_flights.  

### Выбор метода секционирования ###
Поскольку в таблице ticket_flights нет столбцов с типом дата или со значеними из некоторого диапазона, то выбирается hash-секционирование.  
Секционирование будет выполняться по полям первичного ключа таблицы: ticket_no, flight_id.

### Создание секционированной таблицы с полями и ограничениями исходной таблицы ###

set schema 'bookings';  

select * from current_schema();  

drop table if exists ticket_flights_hash;  

create table ticket_flights_hash (  
	ticket_no bpchar(13) not null,  
	flight_id int4 not null,  
	fare_conditions varchar(10) not null,  
	amount numeric(10, 2) not null,  
	constraint ticket_flights_amount_check2 check ((amount >= (0)::numeric)),  
	constraint ticket_flights_fare_conditions_check2 check (((fare_conditions)::text = any (array[('Economy'::character varying)::text, ('Comfort'::character varying)::text, ('Business'::character varying)::text]))),  
	constraint ticket_flights_pkey2 primary key (ticket_no, flight_id),  
	constraint ticket_flights_flight_id_fkey2 foreign key (flight_id) references flights(flight_id),  
	constraint ticket_flights_ticket_no_fkey2 foreign key (ticket_no) references tickets(ticket_no)  
)  
partition by hash(ticket_no, flight_id);  

Для подготовки скрипта по полям и ограничениям исходной таблицы использовал DBeaver.

### Создание секций ###

create table ticket_flights_p0  
  partition of ticket_flights_hash for values with (modulus 3, remainder 0);  
create table ticket_flights_p1   
  partition of ticket_flights_hash for values with (modulus 3, remainder 1);  
create table ticket_flights_p2  
  partition of ticket_flights_hash for values with (modulus 3, remainder 2);  

### Заполнение новой таблицы  ###

insert into ticket_flights_hash (select * from ticket_flights);  

### Перенаправление внешнего ключа на новую таблицу ###

alter table boarding_passes drop constraint boarding_passes_ticket_no_fkey;  
alter table boarding_passes add constraint  
  boarding_passes_ticket_no_fkey foreign key (ticket_no,flight_id)  
  references ticket_flights_hash(ticket_no,flight_id);  

### Удаление старой таблицы и переименование новой ###

drop table ticket_flights;  

alter table ticket_flights_hash rename to ticket_flights;  

### Проверка заполнения новой таблицы и распределения строк по секциям ###

select * from ticket_flights;  

select tableoid::regclass as partition, count(*) from ticket_flights group by tableoid;  

partition | count  
--------- | -----
ticket_flights_p0 | 347331  
ticket_flights_p1 | 349086  
ticket_flights_p2 | 349309  

### Просмотр секционированных объектов ### 

\dP+  
                                       List of partitioned relations
  Schema  |         Name         |  Owner   |       Type        |     Table      | Total size | Description 
--------- | -------------------- | -------- | ----------------- | -------------- | ---------- | ------------
 bookings | ticket_flights       | postgres | partitioned table |                | 68 MB      | 
 bookings | ticket_flights_pkey2 | postgres | partitioned index | ticket_flights | 54 MB      | 


