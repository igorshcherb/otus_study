create table sysinfo (
	sys_name varchar(20),
	sys_descr varchar(200)
);
comment on table sysinfo is 'Информация о системе';
comment on column sysinfo.sys_name is 'Наименование системы';
comment on column sysinfo.sys_descr is 'Описание системы';

-- для первой регистрирующей системы:
insert into sysinfo values ('regsys1', 'Регистрирующая система № 1');

-- для второй регистрирующей системы:
insert into sysinfo values ('regsys2', 'Регистрирующая система № 2');

-- для хранилища данных:
insert into sysinfo values ('dwh', 'Хранилище данных');

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

