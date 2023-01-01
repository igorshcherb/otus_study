## Скрипт для создания витрин ##

### Витрина Договоры-Клиенты-Платежи ###
 
create table dm_contract_payments(  
   id            varchar(100)  
  ,fk_customers  varchar(100)  
  ,con_date      timestamp  
  ,period_start  date  
  ,period_end    date  
  ,customer_name varchar(100)  
  ,customer_type varchar(1)  
  ,customer_desc varchar(1000)  
  ,payment_cnt   int  
  ,payment_sum   numeric(30, 2)  
  ,load_date     date  
);  

comment on table dm_contract_payments is 'Витрина Договоры-Клиенты-Платежи';  
comment on column dm_contract_payments.id            is 'Суррогатный ключ договора';  
comment on column dm_contract_payments.fk_customers  is 'Суррогатный ключ клиента';  
comment on column dm_contract_payments.con_date      is 'Момент времени заключения договора';  
comment on column dm_contract_payments.period_start  is 'Начало периода действия договора';  
comment on column dm_contract_payments.period_end    is 'Окончание периода действия договора';  
comment on column dm_contract_payments.customer_name is 'Наименование клиента';  
comment on column dm_contract_payments.customer_type is 'Тип клиента: 1 - ЮЛ, 2 - ФЛ, 3 - ПБОЮЛ';  
comment on column dm_contract_payments.customer_desc is 'Описание клиента';  
comment on column dm_contract_payments.payment_cnt   is 'Количество платежей по договору';  
comment on column dm_contract_payments.payment_sum   is 'Общая сумма платежей по договору';  
comment on column dm_contract_payments.load_date     is 'Дата загрузки строки в витрину';  

### Витрина Проводки-Платежи-Договоры-Клиенты ###

create table dm_entries_customers(   
   id            varchar(100)  
  ,fk_payments   varchar(100)   
  ,debit         varchar(100)  
  ,credit        varchar(100)  
  ,ent_date      timestamp   
  ,ent_summa     numeric(30, 2)  
  ,purpose       varchar(1000)  
  ,pay_date      timestamp  
  ,fk_contracts  varchar(100)  
  ,fk_customers  varchar(100)  
  ,con_date      timestamp   
  ,period_start  date   
  ,period_end    date  
  ,customer_name varchar(100)  
  ,customer_type varchar(1)  
  ,customer_desc varchar(1000)  
  ,load_date     date  
);  

comment on table dm_entries_customers is 'Витрина Проводки-Платежи-Договоры-Клиенты';   
comment on column dm_entries_customers.id            is 'Суррогатный ключ проводки';  
comment on column dm_entries_customers.fk_payments   is 'Суррогатный ключ платежа';  
comment on column dm_entries_customers.debit         is 'Счет дебета';  
comment on column dm_entries_customers.credit        is 'Счет кредита';  
comment on column dm_entries_customers.ent_date      is 'Дата проводки';  
comment on column dm_entries_customers.ent_summa     is 'Сумма проводки';  
comment on column dm_entries_customers.purpose       is 'Назначение платежа';  
comment on column dm_entries_customers.pay_date      is 'Дата платежа';  
comment on column dm_entries_customers.fk_contracts  is 'Суррогатный ключ договора';  
comment on column dm_entries_customers.fk_customers  is 'Суррогатный ключ клиента';  
comment on column dm_entries_customers.con_date      is 'Момент времени заключения договора';  
comment on column dm_entries_customers.period_start  is 'Начало периода действия договора';  
comment on column dm_entries_customers.period_end    is 'Окончание периода действия договора';  
comment on column dm_entries_customers.customer_name is 'Наименование клиента';  
comment on column dm_entries_customers.customer_type is 'Тип клиента: 1 - ЮЛ, 2 - ФЛ, 3 - ПБОЮЛ';  
comment on column dm_entries_customers.customer_desc is 'Описание клиента';  
comment on column dm_entries_customers.load_date     is 'Дата загрузки строки в витрину';  


