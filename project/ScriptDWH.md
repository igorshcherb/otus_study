## Скрипт для создания объектов БД хранилища данных ##

### Удаление таблиц перед пересозданием ###

drop table if exists dm_contract_payments;  
drop table if exists dm_entries_customers;  
drop table if exists dwh_entries;  
drop table if exists dwh_payments;  
drop table if exists dwh_contracts;  
drop table if exists dwh_customers;  
drop table if exists sysinfo;  


### Информация о системе ###

create table sysinfo (   
   sys_name varchar(20)  
  ,sys_descr varchar(200)  
);  
comment on table sysinfo is 'Информация о системе';  
comment on column sysinfo.sys_name is 'Наименование системы';  
comment on column sysinfo.sys_descr is 'Описание системы';  

-- для хранилища данных:  
insert into sysinfo values ('dwh', 'Хранилище данных');  

### Клиенты ###

create table dwh_customers(   
   id          varchar(100)  
  ,name        varchar(100)  
  ,type        varchar(1)  
  ,description varchar(1000)  
  ,actual_from date  
  ,actual_to   date  
);   
comment on table dwh_customers is 'Клиенты';  
comment on column dwh_customers.id          is 'Суррогатный ключ клиента';  
comment on column dwh_customers.name        is 'Наименование клиента';  
comment on column dwh_customers.type        is 'Тип клиента: 1 - ЮЛ, 2 - ФЛ, 3 - ПБОЮЛ';  
comment on column dwh_customers.description is 'Описание клиента';  
comment on column dwh_customers.actual_from is 'Начало периода актуальности';  
comment on column dwh_customers.actual_to   is 'Окончание периода актуальности';  

alter table dwh_customers add constraint pk_customers primary key (id);  

### Договоры ###

create table dwh_contracts(  
   id           varchar(100)  
  ,fk_customers varchar(100)  
  ,con_date     timestamp  
  ,period_start date  
  ,period_end   date  
  ,actual_from  date  
  ,actual_to    date  
);  
comment on table dwh_contracts is 'Договоры';   
comment on column dwh_contracts.id           is 'Суррогатный ключ договора';  
comment on column dwh_contracts.fk_customers is 'Суррогатный ключ клиента';   
comment on column dwh_contracts.con_date     is 'Момент времени заключения договора';  
comment on column dwh_contracts.period_start is 'Начало периода действия договора';  
comment on column dwh_contracts.period_end   is 'Окончание периода действия договора';  
comment on column dwh_contracts.actual_from is 'Начало периода актуальности';  
comment on column dwh_contracts.actual_to   is 'Окончание периода актуальности';  

alter table dwh_contracts add constraint pk_contracts primary key (id);  
alter table dwh_contracts add constraint fk_contracts_customers foreign key (fk_customers)  
  references dwh_customers(id);  

### Платежи ###

create table dwh_payments(  
   id           varchar(100)  
  ,fk_contracts varchar(100)  
  ,pay_date     timestamp  
  ,summa        numeric(30, 2)  
  ,description  varchar(1000)
  ,actual_from  date  
  ,actual_to    date  
);  
comment on table dwh_payments is 'Платежи';  
comment on column dwh_payments.id           is 'Суррогатный ключ платежа';  
comment on column dwh_payments.fk_contracts is 'Суррогатный ключ договора';  
comment on column dwh_payments.pay_date     is 'Дата платежа';  
comment on column dwh_payments.summa        is 'Сумма платежа';  
comment on column dwh_payments.description  is 'Описание';  
comment on column dwh_payments.actual_from is 'Начало периода актуальности';  
comment on column dwh_payments.actual_to   is 'Окончание периода актуальности';  

alter table dwh_payments add constraint pk_payments primary key (id);   
alter table dwh_payments add constraint fk_payments_contracts foreign key (fk_contracts)   
  references dwh_contracts(id);  

### Бухгалтерские проводки ###

create table dwh_entries(  
   id          varchar(100)  
  ,fk_payments varchar(100)  
  ,debit       varchar(100)  
  ,credit      varchar(100)  
  ,ent_date    timestamp   
  ,ent_summa   numeric(30, 2)  
  ,purpose     varchar(1000)  
  ,actual_from date  
  ,actual_to   date  
);   

comment on table dwh_entries is 'Бухгалтерские проводки';  
comment on column dwh_entries.id           is 'Суррогатный ключ проводки';  
comment on column dwh_entries.fk_payments  is 'Суррогатный ключ платежа';  
comment on column dwh_entries.debit        is 'Счет дебета';  
comment on column dwh_entries.credit       is 'Счет кредита';  
comment on column dwh_entries.ent_date     is 'Дата проводки';  
comment on column dwh_entries.ent_summa    is 'Сумма проводки';  
comment on column dwh_entries.purpose      is 'Назначение платежа';  
comment on column dwh_entries.actual_from  is 'Начало периода актуальности';  
comment on column dwh_entries.actual_to    is 'Окончание периода актуальности';  

alter table dwh_entries add constraint pk_entries primary key (id);  
alter table dwh_entries add constraint fk_entries_payments foreign key (fk_payments)  
  references dwh_payments(id);  
