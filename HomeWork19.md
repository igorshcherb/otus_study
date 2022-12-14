## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 19. Сбор и использование статистики. ##  
# Домашнее задание # 

>1 вариант:  
>Создать индексы на БД, которые ускорят доступ к данным.  
>В данном задании тренируются навыки:  
> * определения узких мест,  
> * написания запросов для создания индекса,  
> * оптимизации.  

>Необходимо:  
> * Создать индекс к какой-либо из таблиц вашей БД.  
> * Прислать текстом результат команды explain, в которой используется данный индекс.  
> * Реализовать индекс для полнотекстового поиска.  
> * Реализовать индекс на часть таблицы или индекс на поле с функцией.  
> * Создать индекс на несколько полей.  
> * Написать комментарии к каждому из индексов.  
> * Описать что и как делали и с какими проблемами столкнулись.  

### Таблицы ###
create table customers(  
   cust_id     bigint  
  ,name        varchar(100)  
  ,type        varchar(1)  
  ,description varchar(1000)  
);  
comment on table customers is 'Клиенты';  
comment on column customers.cust_id     is 'Идентификатор клиента';  
comment on column customers.name        is 'Наименование клиента';  
comment on column customers.type        is 'Тип клиента: 1 - ЮЛ, 2 - ФЛ, 3 - ПБОЮЛ';  
comment on column customers.description is 'Описание клиента';  

alter table customers add constraint pk_customers primary key (cust_id);  

create table contracts(  
   con_id       bigint  
  ,cust_id      bigint  
  ,con_date     timestamp  
  ,period_start date  
  ,period_end   date  
);  
comment on table contracts is 'Договоры';  
comment on column contracts.con_id       is 'Идентификатор договора';  
comment on column contracts.cust_id      is 'Идентификатор клиента';  
comment on column contracts.con_date     is 'Момент времени заключения договора';  
comment on column contracts.period_start is 'Начало периода действия договора';  
comment on column contracts.period_end   is 'Окончание периода действия договора';  

alter table contracts add constraint pk_contracts primary key (con_id);  
alter table contracts add constraint fk_contracts_customers foreign key (cust_id)  
  references customers(cust_id);  

### Заполнение таблиц данными ###

insert into customers values  
  (1, 'Клиент 1', '1', 'vip_клиент'),  
  (2, 'Клиент 2', '1', 'надежный_клиент'),  
  (3, 'Клиент 3', '2', 'vip_клиент, надежный_клиент'),  
  (4, 'Клиент 4', '2', 'vip_клиент'),  
  (5, 'Клиент 5', '3', ''),  
  (6, 'Клиент 6', '1', ''),  
  (7, 'Клиент 6', '2', 'надежный_клиент'); 
  
insert into customers  
 (select generate_series(8, 10000) as cust_id,  
    md5(random()::text)::varchar(10) as name,  
    floor((random()*(4 - 1) + 1)) as type,  
    md5(random()::text)::varchar(20) as description);  
 
analyze customers;  

insert into contracts values  
  (1, 1, '2022-01-20 14:05:03.123456'::timestamp, '2022-02-01'::date, '2022-12-31'::date),  
  (2, 2, '2022-02-15 13:33:43.123456'::timestamp, '2022-02-16'::date, '2022-12-31'::date),   
  (3, 3, '2022-03-17 17:26:25.123456'::timestamp, '2022-03-18'::date, '2022-12-31'::date),   
  (4, 6, '2022-04-01 11:57:06.123456'::timestamp, '2022-04-01'::date, '2022-12-31'::date),   
  (5, 7, '2022-05-12 15:07:32.123456'::timestamp, '2022-06-01'::date, '2022-12-31'::date);  

 analyze contracts;   

### Создание индекса и отражение его в плане выполнения запроса ###

create index ind_customers_type on customers(type);  

analyze customers;  

explain  
  select count(*) from customers where type = '2';  

Aggregate  (cost=82.92..82.94 rows=1 width=8)  
  ->  Index Only Scan using ind_customers_type on customers  (cost=0.29..74.59 rows=3332 width=0)  
        Index Cond: (type = '2'::text)  

### Индекс для полнотекстового поиска ###

create index idx_gin_customers on customers  
using gin (to_tsvector('russian', "description"));  

### Индекс на поле с функцией ### 

create index ind_contracts_date on contracts(date_trunc('day', con_date));  

### Индекс на несколько полей ###

create index ind_contracts_period on contracts(period_start, period_end);

>2 вариант:  
>В результате выполнения ДЗ вы научитесь пользоваться различными вариантами соединения таблиц.  
>В данном задании тренируются навыки написания запросов с различными типами соединений.  
>Необходимо:  
> * Реализовать прямое соединение двух или более таблиц.  
> * Реализовать левостороннее (или правостороннее) соединение двух или более таблиц.  
> * Реализовать кросс соединение двух или более таблиц.  
> * Реализовать полное соединение двух или более таблиц.  
> * Реализовать запрос, в котором будут использованы разные типы соединений.  

>Сделать комментарии на каждый запрос.  
>К работе приложить структуру таблиц, для которых выполнялись соединения.  

### Прямое соединение ###
select  
  con.con_id,  
  cust.name  
from contracts con  
  join customers cust on cust.cust_id = con.cust_id;  

### Левостороннее соединение ###
select  
  con.con_id,  
  cust.name  
from contracts con  
  left join customers cust on cust.cust_id = con.cust_id;  

### Кросс соединение ###
select  
  con.con_id,  
  cust.name  
from contracts con  
  cross join customers cust;  
  
### Полное соединение ### 
select  
  con.con_id,  
  cust.name  
from contracts con  
  full join customers cust on cust.cust_id = con.cust_id;  
  
### Запрос с разными типами соединений ###  
select  
  con.con_id,  
  cust.name,  
  con2.con_id  
from contracts con  
  join customers cust on cust.cust_id = con.cust_id  
  left join contracts con2 on con2.con_date between con.period_start and con.period_end;  

### Структура таблиц, для которых выполнялись соединения ###
[customers] <- [contracts]

>Задание со звездочкой*  
>Придумайте 3 своих метрики на основе показанных представлений, отправьте их через ЛК, а так же поделитесь с коллегами в слаке.  

### Метрики, которые я придумал ###

1. Отношение последовательных сканирований таблиц к общему числу сканирований (сканирование таблиц + сканирование индексов):  
select seq_scan / (idx_scan + seq_scan) m1 from pg_stat_user_tables;  
При оптимизации приложения число сканирований таблиц должно уменьшаться, а число сканирований индексов - увеличиваться. Предложенная метрика позволит контролировать результаты оптимизации.  
2. Отношение просмотренных записей к числу записей, возвращенных запросами:  
select  
  case tup_returned  
    when 0 then '--'  
    else (tup_fetched / tup_returned)::varchar  
  end m2 from pg_stat_database;  
Метрика позволит отслеживать ситуации, когда для выполнения запроса требуется просмотреть гораздо больше строк, чем запрос возвращает. В идеале отношение tup_fetched / tup_returned должно быть близко к 1.  
3. Отношение "горячих" апдейтов к общему числу апдейтов. "Горячие" апдейты не затрагивают индексы и потому более эффективны. Чем больше значение данной метрики, тем эффективнее работает приложение на изменение данных.  
select  
  case n_tup_upd  
    when 0 then '--'  
    else (n_tup_hot_upd / n_tup_upd)::varchar  
  end m3 from pg_stat_user_tables;  

### Описание данных метрик представил в слаке. ###
