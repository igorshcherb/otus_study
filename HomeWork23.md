## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 23. Хранимые функции и процедуры, часть 3. ##  
# Домашнее задание # 

>Скрипт и развернутое описание задачи – в ЛК (файл hw_triggers.sql) или по ссылке: https://disk.yandex.ru/d/l70AvknAepIJXQ  
>В БД создана структура, описывающая товары (таблица goods) и продажи (таблица sales).  
>Есть запрос для генерации отчета – сумма продаж по каждому товару.  
>БД была денормализована, создана таблица (витрина), структура которой повторяет структуру отчета.  
>Создать триггер на таблице продаж, для поддержки данных в витрине в актуальном состоянии (вычисляющий при каждой продаже сумму и записывающий её в витрину).  
>Подсказка: не забыть, что кроме INSERT есть еще UPDATE и DELETE. 

С помощью скрипта hw_triggers.sql создал схему pract_functions, таблицы goods, sales, good_sum_mart.  

### Функция для триггера ###

create or replace function pract_functions.sales_mart()  
	returns trigger  
	language plpgsql  
as $function$  
    declare  
	  v_good_name  varchar(63);  
	  v_good_price numeric(12, 2);  
	  v_sum_sale   numeric(16, 2);  
    begin  
	  if (tg_op = 'INSERT') then  
        select good_name, good_price  
        into v_good_name, v_good_price  
        from pract_functions.goods  
        where goods_id = new.good_id;  
        - -  
        v_sum_sale = v_good_price * new.sales_qty;   
        - -   
        update pract_functions.good_sum_mart  
        set sum_sale = sum_sale + v_sum_sale  
        where good_name = v_good_name;  
        - -  
        if not found then  
          insert into pract_functions.good_sum_mart values (v_good_name, v_sum_sale);  
        end if;  
        - -  
        return new;  
      elsif (tg_op = 'DELETE') then  
        select good_name, good_price  
        into v_good_name, v_good_price  
        from pract_functions.goods  
        where goods_id = old.good_id;  
        - -  
        v_sum_sale = v_good_price * old.sales_qty;  
        - -   
        update pract_functions.good_sum_mart  
        set sum_sale = sum_sale - v_sum_sale  
        where good_name = v_good_name;  
        - -  
        return old;
      elsif (tg_op = 'UPDATE') then
        select good_name, good_price
        into v_good_name, v_good_price
        from pract_functions.goods
        where goods_id = old.good_id;
        - -  
        v_sum_sale = v_good_price * old.sales_qty;  
        --  
        update pract_functions.good_sum_mart  
        set sum_sale = sum_sale - v_sum_sale  
        where good_name = v_good_name;  
        - -  
        select good_name, good_price  
        into v_good_name, v_good_price  
        from pract_functions.goods  
        where goods_id = new.good_id;  
        - -  
        v_sum_sale = v_good_price * new.sales_qty;  
        - -   
        update pract_functions.good_sum_mart  
        set sum_sale = sum_sale + v_sum_sale  
        where good_name = v_good_name;  
        - -  
        if not found then  
          insert into pract_functions.good_sum_mart values (v_good_name, v_sum_sale);  
        end if;  
        - -  
        return new;  
      end if;  
  end;  
$function$  
;  

### Триггер ###

create or replace trigger sales_mart_trg  
    before insert or update or delete  
    on pract_functions.sales  
    for each row  
    execute procedure pract_functions.sales_mart();  

### Первоначальное заполнение витрины ###

insert into pract_functions.good_sum_mart (good_name, sum_sale)   
  (select g.good_name, sum(g.good_price * s.sales_qty)  
   from pract_functions.goods g  
     inner join pract_functions.sales s on s.good_id = g.goods_id  
   group by g.good_name);  

### Проверка работы триггера ###
select sum_sale from pract_functions.good_sum_mart where good_name = 'Спички хозайственные';  
65.50  
insert into pract_functions.sales(good_id, sales_qty) values (1, 1);  
select sum_sale from pract_functions.good_sum_mart where good_name = 'Спички хозайственные';  
66.00  
select max(sales_id) from pract_functions.sales;  
11  
update pract_functions.sales set sales_qty = 2 where sales_id = 11;  
select sum_sale from pract_functions.good_sum_mart where good_name = 'Спички хозайственные';  
66.50  
delete from pract_functions.sales where sales_id = 11;  
select sum_sale from pract_functions.good_sum_mart where good_name = 'Спички хозайственные';  
65.50  

-------------------------------------
>Задание со звездочкой*  
>Чем такая схема (витрина+триггер) предпочтительнее отчета, создаваемого "по требованию" (кроме производительности)?  
>Подсказка: В реальной жизни возможны изменения цен.  

Схема витрина+триггер позволяет учитывать продажи по ценам, которые были в момент продажи.  

