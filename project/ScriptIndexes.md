## Скрипт для создания индексов ##

Индексы создаются для ускорения запросов, которые выполняются при загрузке таблиц хранилища,  
загрузки витрин, формировании отчетов.  

create index contracts_date_ind on contracts(con_date);  
create index contracts_period_ind on contracts(period_start, period_end);  
create index customers_type_ind on customers(type);  
create index entries_date_ind on entries(ent_date);  
create index entries_debit_ind on entries(debit);  
create index entries_credit_ind on entries(credit);  
create index payments_date_ind on payments(pay_date);  
--  
create index dwh_contracts_date_ind on dwh_contracts(con_date);  
create index dwh_contracts_period_ind on dwh_contracts(period_start, period_end);  
create index dwh_customers_type_ind on dwh_customers(type);  
create index dwh_entries_date_ind on dwh_entries(ent_date);  
create index dwh_entries_debit_ind on dwh_entries(debit);  
create index dwh_entries_credit_ind on dwh_entries(credit);  
create index dwh_payments_date_ind on dwh_payments(pay_date);  
create index dwh_contracts_actual_ind on dwh_contracts(actual_from, actual_to);  
create index dwh_customers_actual_ind on dwh_customers(actual_from, actual_to);  
create index dwh_entries_actual_ind on dwh_entries(actual_from, actual_to);  
create index dwh_payments_actual_ind on dwh_payments(actual_from, actual_to);  
  