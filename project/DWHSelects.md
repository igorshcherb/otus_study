## Запросы для заполнения таблиц хранилища ##

### Клиенты ###

select  
  ('sys2|' || id) as id  
  ,name  
  ,type  
  ,description  
  ,current_date as actual_from  
  ,'9999-12-31'::date as actual_to  
from customers  

### Договоры ###

select  
  ('sys2|' || id) as id  
  ,('sys2|' || fk_customers) as fk_customers  
  ,con_date  
  ,period_start  
  ,period_end  
  ,current_date as actual_from  
  ,'9999-12-31'::date as actual_to  
from contracts  

### Платежи ###

select  
  ('sys2|' || id) as id  
  ,('sys2|' || fk_contracts) as fk_contracts  
  ,pay_date  
  ,summa  
  ,description  
  ,current_date as actual_from  
  ,'9999-12-31'::date as actual_to  
from payments  

### Проводки ###

select  
  ('sys2|' || id) as id  
  ,('sys2|' || fk_payments) as fk_payments  
  ,debit  
  ,credit  
  ,ent_date  
  ,ent_summa  
  ,purpose  
  ,current_date as actual_from  
  ,'9999-12-31'::date as actual_to  
from entries  
