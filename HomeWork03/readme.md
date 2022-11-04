## PostgreSQL для администраторов баз данных и разработчиков ##
## Занятие 03. Установка PostgreSQL ##
# Домашнее задание #
## 1. Установка PostgreSQL в ВМ ##
## 2. Установка Doker в ВМ ##

docker pull postgres
docker run -itd -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=postgres -p 5432:5432 -v /data:/var/lib/postgresql/data --name postgresql postgres
psql --username=postgres --dbname=postgres
psql (15.0 (Debian 15.0-1.pgdg110+1))
Type "help" for help.

select version();
                                                           version                                                           
-----------------------------------------------------------------------------------------------------------------------------
 PostgreSQL 15.0 (Debian 15.0-1.pgdg110+1) on x86_64-pc-linux-gnu, compiled by gcc (Debian 10.2.1-6) 10.2.1 20210110, 64-bit
(1 row)
 
## 3. Эксперименты с Doker ##
