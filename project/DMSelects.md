## Запросы для заполнения витрин ##

### Витрина Договоры-Клиенты-Платежи ###

with q1 as  
(  
select  
   con.id  
  ,con.fk_customers  
  ,con.con_date  
  ,con.period_start  
  ,con.period_end  
  ,cust.name as customer_name  
  ,cust.type as customer_type  
  ,cust.description as customer_desc  
  ,(select json_build_object(  
     'payment_cnt', count(*)::varchar,   
     'payment_sum', sum(summa)::varchar)   
     from dwh_payments pay where pay.fk_contracts = con.id  
       and current_date between pay.actual_from  
                            and pay.actual_to) pay_json  
  ,current_date as load_date  
from  
  dwh_contracts con  
  left join dwh_customers cust on cust.id = con.fk_customers  
    and current_date between cust.actual_from and cust.actual_to  
where  
  current_date between con.actual_from and con.actual_to  
)  
select  
   q1.id  
  ,q1.fk_customers  
  ,q1.con_date  
  ,q1.period_start  
  ,q1.period_end  
  ,q1.customer_name  
  ,q1.customer_type  
  ,q1.customer_desc  
  ,(q1.pay_json ->> 'payment_cnt')::int as payment_cnt  
  ,(q1.pay_json ->> 'payment_sum')::numeric(30,2) as payment_sum  
  ,load_date  
from q1   

### Витрина Проводки-Платежи-Договоры-Клиенты ###

select  
   ent.id  
  ,ent.fk_payments  
  ,ent.debit  
  ,ent.credit  
  ,ent.ent_date  
  ,ent.ent_summa  
  ,ent.purpose  
  ,pay.pay_date  
  ,pay.fk_contracts  
  ,con.fk_customers  
  ,con.con_date  
  ,con.period_start  
  ,con.period_end  
  ,cust.name as customer_name  
  ,cust.type as customer_type  
  ,cust.description as customer_desc  
  ,current_date as load_date   
from  
  dwh_entries ent   
  left join dwh_payments pay on pay.id = ent.fk_payments  
    and current_date between pay.actual_from and pay.actual_to  
  left join dwh_contracts con on con.id = pay.fk_contracts  
    and current_date between con.actual_from and con.actual_to  
  left join dwh_customers cust on cust.id = con.fk_customers  
    and current_date between cust.actual_from and cust.actual_to  
where  
  current_date between ent.actual_from and ent.actual_to  

