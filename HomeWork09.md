## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 09. Журналы. ##  
# Домашнее задание #  

>Настройте выполнение контрольной точки раз в 30 секунд.

В файле postgresql.conf:  

checkpoint_timeout = 30s  

Перезапустил кластер:  

sudo -i -u postgres  

usr/lib/postgresql/14/bin/pg_ctl start -D /var/lib/postgresql/14/notmain  

>10 минут c помощью утилиты pgbench подавайте нагрузку.

pgbench -P 60 -T 600 -p 5433 -U postgres postgres

pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))  
starting vacuum...end.  
progress: 60.0 s, 714.0 tps, lat 1.400 ms stddev 0.488  
progress: 120.0 s, 707.1 tps, lat 1.414 ms stddev 0.481  
progress: 180.0 s, 828.2 tps, lat 1.207 ms stddev 0.442  
progress: 240.0 s, 852.0 tps, lat 1.174 ms stddev 0.413  
progress: 300.0 s, 771.2 tps, lat 1.296 ms stddev 0.658  
progress: 360.0 s, 719.4 tps, lat 1.390 ms stddev 0.609  
progress: 420.0 s, 844.6 tps, lat 1.184 ms stddev 0.615  
progress: 480.0 s, 716.6 tps, lat 1.395 ms stddev 0.606  
progress: 540.0 s, 719.8 tps, lat 1.389 ms stddev 0.654  
progress: 600.0 s, 782.2 tps, lat 1.278 ms stddev 0.543  
transaction type: <builtin: TPC-B (sort of)>  
scaling factor: 1  
query mode: simple  
number of clients: 1  
number of threads: 1  
duration: 600 s  
number of transactions actually processed: 459311  
latency average = 1.306 ms  
latency stddev = 0.563 ms  
initial connection time = 2.618 ms  
tps = 765.521065 (without initial connection time)  

>Измерьте, какой объем журнальных файлов был сгенерирован за это время.

ls -hl /var/lib/postgresql/14/notmain/pg_wal  
total 305M  
-rw------- 1 postgres postgres  16M ноя 20 18:54 000000010000000000000055  
-rw------- 1 postgres postgres  16M ноя 20 18:54 000000010000000000000056  
-rw------- 1 postgres postgres  16M ноя 20 18:55 000000010000000000000057  
-rw------- 1 postgres postgres  16M ноя 20 18:48 000000010000000000000058  
-rw------- 1 postgres postgres  16M ноя 20 18:48 000000010000000000000059  
-rw------- 1 postgres postgres  16M ноя 20 18:49 00000001000000000000005A  
-rw------- 1 postgres postgres  16M ноя 20 18:49 00000001000000000000005B  
-rw------- 1 postgres postgres  16M ноя 20 18:49 00000001000000000000005C  
-rw------- 1 postgres postgres  16M ноя 20 18:50 00000001000000000000005D  
-rw------- 1 postgres postgres  16M ноя 20 18:50 00000001000000000000005E  
-rw------- 1 postgres postgres  16M ноя 20 18:50 00000001000000000000005F  
-rw------- 1 postgres postgres  16M ноя 20 18:51 000000010000000000000060  
-rw------- 1 postgres postgres  16M ноя 20 18:51 000000010000000000000061  
-rw------- 1 postgres postgres  16M ноя 20 18:52 000000010000000000000062  
-rw------- 1 postgres postgres  16M ноя 20 18:52 000000010000000000000063  
-rw------- 1 postgres postgres  16M ноя 20 18:53 000000010000000000000064  
-rw------- 1 postgres postgres  16M ноя 20 18:52 000000010000000000000065  
-rw------- 1 postgres postgres  16M ноя 20 18:53 000000010000000000000066  
-rw------- 1 postgres postgres  16M ноя 20 18:53 000000010000000000000067  

**16M * 19 = 304 M**  
 
>Оцените, какой объем приходится в среднем на одну контрольную точку.

**16M**  

>Проверьте данные статистики: все ли контрольные точки выполнялись точно по расписанию.

psql -U postgresql -p 5433

postgres=# SELECT * FROM pg_stat_bgwriter \gx  
-[ RECORD 1 ]---------+------------------------------  
checkpoints_timed     | 63  
checkpoints_req       | 0  
checkpoint_write_time | 538880  
checkpoint_sync_time  | 381  
buffers_checkpoint    | 38973  
buffers_clean         | 0  
maxwritten_clean      | 0  
buffers_backend       | 2946  
buffers_backend_fsync | 0  
buffers_alloc         | 5284  
stats_reset           | 2022-11-20 18:37:50.984922+03  

checkpoints_timed — по расписанию (по достижению checkpoint_timeout),  
checkpoints_req — по требованию.  

Все котрольные точки выполнялись по расписанию.
 
>Почему так произошло?

**Задана высокая частота контрольных точек - раз в 30 сек**  

>Сравните tps в синхронном/асинхронном режиме утилитой pgbench. 
>Объясните полученный результат.
>Создайте новый кластер с включенной контрольной суммой страниц. 
>Создайте таблицу. Вставьте несколько значений. 
>Выключите кластер. Измените пару байт в таблице. 
>Включите кластер и сделайте выборку из таблицы. 
>Что и почему произошло? как проигнорировать ошибку и продолжить работу?
