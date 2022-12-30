## Скрипт для создания объектов БД регистрирующих систем ##

### Информация о системе ###

create table sysinfo (  
   sys_name varchar(20),  
  ,sys_descr varchar(200)  
);  
comment on table sysinfo is 'Информация о системе';  
comment on column sysinfo.sys_name is 'Наименование системы';  
comment on column sysinfo.sys_descr is 'Описание системы';  

-- для первой регистрирующей системы:  
insert into sysinfo values ('regsys1', 'Регистрирующая система № 1');  

-- для второй регистрирующей системы:  
insert into sysinfo values ('regsys2', 'Регистрирующая система № 2');  

-- для третьей регистрирующей системы:  
insert into sysinfo values ('regsys3', 'Регистрирующая система № 3');  

-- для хранилища данных:  
insert into sysinfo values ('dwh', 'Хранилище данных');  

### Клиенты ###

create table customers(   
   id          bigint  
  ,name        varchar(100)  
  ,type        varchar(1)  
  ,description varchar(1000)  
);  
comment on table customers is 'Клиенты';  
comment on column customers.id          is 'Идентификатор клиента';  
comment on column customers.name        is 'Наименование клиента';  
comment on column customers.type        is 'Тип клиента: 1 - ЮЛ, 2 - ФЛ, 3 - ПБОЮЛ';  
comment on column customers.description is 'Описание клиента';  

alter table customers add constraint pk_customers primary key (id);  

### Договоры ###

create table contracts(  
   id           bigint  
  ,fk_customers bigint  
  ,con_date     timestamp  
  ,period_start date  
  ,period_end   date  
);  
comment on table contracts is 'Договоры';  
comment on column contracts.id           is 'Идентификатор договора';  
comment on column contracts.fk_customers is 'Идентификатор клиента';  
comment on column contracts.con_date     is 'Момент времени заключения договора';  
comment on column contracts.period_start is 'Начало периода действия договора';  
comment on column contracts.period_end   is 'Окончание периода действия договора';  

alter table contracts add constraint pk_contracts primary key (id);  
alter table contracts add constraint fk_contracts_customers foreign key (fk_customers)  
  references customers(id);  

### Платежи ###

create table payments(  
   id           bigint  
  ,fk_contracts bigint  
  ,pay_date     timestamp  
  ,summa        numeric  
  ,description  varchar(1000)  
);  
comment on table payments is 'Платежи';  
comment on column payments.id           is 'Идентификатор платежа';  
comment on column payments.fk_contracts is 'Идентификатор договора';  
comment on column payments.pay_date     is 'Дата платежа';  
comment on column payments.summa        is 'Сумма платежа';  
comment on column payments.description  is 'Описание';  

alter table payments add constraint pk_payments primary key (id);   
alter table payments add constraint fk_payments_contracts foreign key (fk_contracts)   
  references contracts(id);  

### Бухгалтерские проводки ###

create table entries(  
   id          bigint  
  ,fk_payments bigint   
  ,debit       varchar(100)  
  ,credit      varchar(100)  
  ,ent_date    timestamp   
  ,ent_summa   numeric  
  ,purpose     varchar(1000)  
);  

comment on table entries is 'Бухгалтерские проводки';  
comment on column entries.id           is 'Идентификатор проводки';  
comment on column entries.fk_payments  is 'Идентификатор платежа';  
comment on column entries.debit        is 'Счет дебета';  
comment on column entries.credit       is 'Счет кредита';  
comment on column entries.ent_date     is 'Дата проводки';  
comment on column entries.ent_summa    is 'Сумма проводки';  
comment on column entries.purpose      is 'Назначение платежа';  

alter table entries add constraint pk_entries primary key (id);  
alter table entries add constraint fk_entries_payments foreign key (fk_payments)  
  references payments(id);  

