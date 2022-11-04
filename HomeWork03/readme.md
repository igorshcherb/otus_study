## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 03. Установка PostgreSQL ##  
# Домашнее задание #  

>создать ВМ с Ubuntu 20.04/22.04 или развернуть докер любым удобным способом  
>поставить на нем Docker Engine  

**выполнил**  

>сделать каталог /var/lib/postgres  
>развернуть контейнер с PostgreSQL 14 смонтировав в него /var/lib/postgres  

**docker pull postgres**  

**docker run -itd -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 -v /data:/var/lib/postgresql/data --name postgresql postgres**  

**# psql --username=postgres --dbname=postgres**  

**psql (15.0 (Debian 15.0-1.pgdg110+1))**  

**Type "help" for help.**  

**postgres=# select version();**  

**version**  
**-----------------------------------------------------------------------------------------------------------------------------**  
**PostgreSQL 15.0 (Debian 15.0-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit**  
**(1 row)**  
 
>подключится к контейнеру с сервером и сделать таблицу с парой строк  

**create table tab1(id int8, vc varchar(100));**  
**insert into tab1(id, vc) values (1, 'one');**  
**insert into tab1(id, vc) values (2, 'two');**  
**select * from tab1;**  

>подключится к контейнеру с сервером с ноутбука/компьютера извне инстансов GCP/ЯО/места установки докера  

**подключился из DBeaver**

>удалить контейнер с сервером

**выполнил**  

>создать его заново

**docker run -itd -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 -v /data:/var/lib/postgresql/data --name postgresql postgres**  

>подключится снова из контейнера с клиентом к контейнеру с сервером  
>проверить, что данные остались на месте  

**# psql --username=postgres --dbname=postgres**  
**psql (15.0 (Debian 15.0-1.pgdg110+1))**  
**Type "help" for help.**  

**postgres=# select * from tab1;**  
** id | vc  **  
**----+-----**  
**  1 | one**  
**  2 | two**  
**(2 rows)**  

