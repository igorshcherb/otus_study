insert into customers  
 (select generate_series(8, 10000) as cust_id,  
    md5(random()::text)::varchar(10) as name,  
    floor((random()*(4 - 1) + 1)) as type,  
    md5(random()::text)::varchar(20) as description);  

