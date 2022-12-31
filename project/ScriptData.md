## Скрипт для заполнения таблиц БД тестовыми данными ##

### Клиенты ###

-- delete from customers;  
insert into customers   
 (select generate_series(1, 10000) as id,  
    md5(random()::text)::varchar(10) as name,  
    floor((random()*(4 - 1) + 1)) as type,  
    md5(random()::text)::varchar(20) as description);  
   
-- select count(*) from customers; -- 10000  
-- select * from customers;  
-- select min(type), max(type) from customers;  

### Договоры ###

-- delete from contracts;  
insert into contracts   
 (with id_date as  
    (select generate_series(1, 20000) as id,  
           ('2023-01-01'::date + floor((random()*365))::integer) as con_date)  
 select id_date.id,  
    floor((random()*10000 + 1)) as fk_customers,    
    id_date.con_date,  
    id_date.con_date as period_start,  
    '2023-12-31'::date as period_end  
 from id_date)   

-- select count(*) from contracts; -- 20000  
-- select * from contracts;  
-- select min(fk_customers), max(fk_customers) from contracts;  
-- select min(con_date), max(con_date) from contracts;  
 
### Платежи ###

-- delete from payments;  
insert into payments  
(with id_contr as  
 (select generate_series(1, 40000) as id,  
    floor((random()*20000 + 1)) as fk_contracts)  
 ,  
 id_contr_date as  
 (select  
    id_contr.id,  
    id_contr.fk_contracts,  
    (select con_date from contracts where id = id_contr.fk_contracts) con_date  
  from id_contr)  
 select  
   id_contr_date.id,  
   id_contr_date.fk_contracts,  
   (id_contr_date.con_date + (('2023-12-31'::date - id_contr_date.con_date)*random())) as pay_date,  
   random()*100000 as summa,  
   md5(random()::text)::varchar(20) as description  
 from id_contr_date  
);  

-- select count(*) from payments; -- 40000  
-- select * from payments;  
-- select min(fk_contracts), max(fk_contracts) from payments;  
-- select min(pay_date), max(pay_date) from payments;  
 
### Бухгалтерские проводки ###

-- delete from entries;  
insert into entries  
(with id_pay as  
(select generate_series(1, 80000) as id,  
floor((random()*40000 + 1)) as fk_payments)  
,  
 id_pay_json as  
 (select  
    id_pay.id,  
    id_pay.fk_payments,  
    (select json_build_object('pay_date', pay_date::varchar, 'summa', summa::varchar)  
     from payments where id = id_pay.fk_payments) pay_json  
  from id_pay)  
 select  
   id_pay_json.id as id,  
   id_pay_json.fk_payments as fk_payments,  
   'Счет' || floor((random()*(10 - 1) + 1))::varchar as debit,  
   'Счет' || floor((random()*(10 - 1) + 1))::varchar as credit,  
   (id_pay_json.pay_json ->> 'pay_date')::timestamp as ent_date,   
   (id_pay_json.pay_json ->> 'summa')::numeric as ent_summa,   
   md5(random()::text)::varchar(20) as purpose  
 from id_pay_json    
);  

-- select count(*) from entries; -- 80000  
-- select * from entries;  




