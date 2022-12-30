## Скрипт для создания объектов БД хранилища данных ##

### Информация о системе ###

create table sysinfo (  
   sys_name varchar(20),  
  ,sys_descr varchar(200)  
);  
comment on table sysinfo is 'Информация о системе';  
comment on column sysinfo.sys_name is 'Наименование системы';  
comment on column sysinfo.sys_descr is 'Описание системы';  

-- для хранилища данных:  
insert into sysinfo values ('dwh', 'Хранилище данных');  

### Клиенты ###

create table customers(   
   id          varchar(100) 
  ,name        varchar(100)  
  ,type        varchar(1)  
  ,description varchar(1000)  
);  
comment on table customers is 'Клиенты';  
comment on column customers.id          is 'Суррогатный ключ клиента';  
comment on column customers.name        is 'Наименование клиента';  
comment on column customers.type        is 'Тип клиента: 1 - ЮЛ, 2 - ФЛ, 3 - ПБОЮЛ';  
comment on column customers.description is 'Описание клиента';  

alter table customers add constraint pk_customers primary key (id);  

### Договоры ###

create table contracts(  
   id           varchar(100)  
  ,fk_customers varchar(100)  
  ,con_date     timestamp  
  ,period_start date  
  ,period_end   date  
);  
comment on table contracts is 'Договоры';  
comment on column contracts.id           is 'Суррогатный ключ договора';  
comment on column contracts.fk_customers is 'Суррогатный ключ клиента';  
comment on column contracts.con_date     is 'Момент времени заключения договора';  
comment on column contracts.period_start is 'Начало периода действия договора';  
comment on column contracts.period_end   is 'Окончание периода действия договора';  

alter table contracts add constraint pk_contracts primary key (id);  
alter table contracts add constraint fk_contracts_customers foreign key (fk_customers)  
  references customers(id);  

### Платежи ###

create table payments(  
   id           varchar(100)  
  ,fk_contracts varchar(100)  
  ,pay_date     timestamp  
  ,summa        numeric  
  ,description  varchar(1000)  
);  
comment on table payments is 'Платежи';  
comment on column payments.id           is 'Суррогатный ключ платежа';  
comment on column payments.fk_contracts is 'Суррогатный ключ договора';  
comment on column payments.pay_date     is 'Дата платежа';  
comment on column payments.summa        is 'Сумма платежа';  
comment on column payments.description  is 'Описание';  

alter table payments add constraint pk_payments primary key (id);   
alter table payments add constraint fk_payments_contracts foreign key (fk_contracts)   
  references contracts(id);  

### Бухгалтерские проводки ###

create table entries(  
   id          varchar(100) 
  ,fk_payments varchar(100)  
  ,debit       varchar(100)  
  ,credit      varchar(100)  
  ,ent_date    timestamp   
  ,ent_summa   numeric  
  ,purpose     varchar(1000)  
);  

comment on table entries is 'Бухгалтерские проводки';  
comment on column entries.id           is 'Суррогатный ключ проводки';  
comment on column entries.fk_payments  is 'Суррогатный ключ платежа';  
comment on column entries.debit        is 'Счет дебета';  
comment on column entries.credit       is 'Счет кредита';  
comment on column entries.ent_date     is 'Дата проводки';  
comment on column entries.ent_summa    is 'Сумма проводки';  
comment on column entries.purpose      is 'Назначение платежа';  

alter table entries add constraint pk_entries primary key (id);  
alter table entries add constraint fk_entries_payments foreign key (fk_payments)  
  references payments(id);  

