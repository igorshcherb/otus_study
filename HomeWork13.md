## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 13. Настройка PostgreSQL. ##  
# Домашнее задание # 

>развернуть виртуальную машину любым удобным способом  
>поставить на неё PostgreSQL 14 любым способом  
>настроить кластер PostgreSQL 14 на максимальную производительность  
>не обращая внимание на возможные проблемы с надежностью в случае  
>аварийной перезагрузки виртуальной машины  
>нагрузить кластер через утилиту  
>https://github.com/Percona-Lab/sysbench-tpcc   
>(требует установки https://github.com/akopytov/sysbench)   
>или через утилиту pgbench (https://postgrespro.ru/docs/postgrespro/14/pgbench)  
>написать какого значения tps удалось достичь, показать какие параметры в какие значения устанавливали и почему  

### Инициализировал pgbench ###
pgbench -i postgres  

### Проверил производительность сервера с параметрами по умолчанию ###
select name, setting from pg_settings where name in  
('max_connections','shared_buffers','effective_cache_size','maintenance_work_mem',  
'checkpoint_completion_target','wal_buffers','default_statistics_target','random_page_cost',  
'effective_io_concurrency','work_mem','min_wal_size','max_wal_size');  
             name             | setting   
------------------------------+---------  
 checkpoint_completion_target | 0.9  
 default_statistics_target    | 100  
 effective_cache_size         | 524288  
 effective_io_concurrency     | 1  
 maintenance_work_mem         | 65536  
 max_connections              | 100  
 max_wal_size                 | 1024  
 min_wal_size                 | 80  
 random_page_cost             | 4  
 shared_buffers               | 16384  
 wal_buffers                  | 512  
 work_mem                     | 4096  


### Легкий тест ###
postgres@Virtual1:~$ pgbench -c 50 -j 2 -P 10 -T 30 -U postgres postgres  
pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))  
starting vacuum...end.  
progress: 10.0 s, 851.8 tps, lat 57.893 ms stddev 54.174  
progress: 20.0 s, 865.7 tps, lat 57.699 ms stddev 60.311  
progress: 30.0 s, 869.5 tps, lat 57.376 ms stddev 55.506  
transaction type: <builtin: TPC-B (sort of)>  
scaling factor: 1  
query mode: simple  
number of clients: 50  
number of threads: 2  
duration: 30 s  
number of transactions actually processed: 25920  
latency average = 57.785 ms  
latency stddev = 56.823 ms  
initial connection time = 82.410 ms  
tps = 863.297679 (without initial connection time)  

### Более тяжелый тест ###
postgres@Virtual1:~$ pgbench -c 50 -C -j 2 -P 10 -T 30 -M extended -U postgres postgres  
pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))  
starting vacuum...end.  
progress: 10.0 s, 353.0 tps, lat 136.893 ms stddev 120.532  
progress: 20.0 s, 360.4 tps, lat 137.181 ms stddev 125.430  
progress: 30.0 s, 361.9 tps, lat 135.818 ms stddev 130.075  
transaction type: <builtin: TPC-B (sort of)>  
scaling factor: 1  
query mode: extended  
number of clients: 50  
number of threads: 2  
duration: 30 s  
number of transactions actually processed: 10803  
latency average = 136.894 ms  
latency stddev = 125.682 ms  
average connection time = 2.068 ms  
tps = 358.824451 (including reconnection times)  

### Сгенерил значения параметров с помощью pgtune ###
max_connections = 100  
shared_buffers = 1GB  
effective_cache_size = 3GB  
maintenance_work_mem = 256MB  
checkpoint_completion_target = 0.9  
wal_buffers = 16MB  
default_statistics_target = 100  
random_page_cost = 4  
effective_io_concurrency = 2  
work_mem = 2621kB  
min_wal_size = 1GB  
max_wal_size = 4GB  

### Записал параметры в файл /etc/postgresql/14/main/conf.d/pgtune.conf ### 
### В файле /etc/postgresql/14/main/postgresql.conf include_dir = 'conf.d' ###

systemctl restart postgresql  

### Легкий тест ###
pgbench -c 50 -j 2 -P 10 -T 30 -U postgres postgres  
pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))  
starting vacuum...end.  
progress: 10.0 s, 848.8 tps, lat 58.275 ms stddev 59.585  
progress: 20.0 s, 873.1 tps, lat 57.348 ms stddev 57.392  
progress: 30.0 s, 885.0 tps, lat 56.271 ms stddev 56.061  
transaction type: <builtin: TPC-B (sort of)>  
scaling factor: 1  
query mode: simple  
number of clients: 50  
number of threads: 2  
duration: 30 s  
number of transactions actually processed: 26120  
latency average = 57.418 ms  
latency stddev = 57.819 ms  
initial connection time = 44.734 ms  
tps = 868.742836 (without initial connection time)  

### Более тяжелый тест ###
pgbench -c 50 -C -j 2 -P 10 -T 30 -M extended -U postgres postgres  
pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))  
starting vacuum...end.  
progress: 10.0 s, 351.0 tps, lat 138.409 ms stddev 121.235  
progress: 20.0 s, 352.9 tps, lat 139.714 ms stddev 133.091  
progress: 30.0 s, 351.7 tps, lat 139.947 ms stddev 116.766  
transaction type: <builtin: TPC-B (sort of)>  
scaling factor: 1  
query mode: extended  
number of clients: 50  
number of threads: 2  
duration: 30 s  
number of transactions actually processed: 10605  
latency average = 139.459 ms  
latency stddev = 123.953 ms  
average connection time = 2.117 ms  
tps = 352.187690 (including reconnection times)  

### Увеличиваем скорость, уменьшая надежность: synchronous_commit = off ### 

### Легкий тест ###
pgbench -c 50 -j 2 -P 10 -T 30 -U postgres postgres  
pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))  
starting vacuum...end.  
progress: 10.0 s, 2326.8 tps, lat 21.298 ms stddev 22.281  
progress: 20.0 s, 2419.7 tps, lat 20.639 ms stddev 20.851  
progress: 30.0 s, 2486.5 tps, lat 20.056 ms stddev 20.518  
transaction type: <builtin: TPC-B (sort of)>  
scaling factor: 1  
query mode: simple  
number of clients: 50  
number of threads: 2  
duration: 30 s  
number of transactions actually processed: 72381  
latency average = 20.673 ms  
latency stddev = 21.240 ms  
initial connection time = 44.405 ms  
tps = 2410.727382 (without initial connection time)  

### Более тяжелый тест ###
pgbench -c 50 -C -j 2 -P 10 -T 30 -M extended -U postgres postgres  
pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))  
starting vacuum...end.  
progress: 10.0 s, 464.8 tps, lat 104.258 ms stddev 72.685  
progress: 20.0 s, 467.5 tps, lat 104.202 ms stddev 81.904  
progress: 30.0 s, 467.1 tps, lat 105.017 ms stddev 79.388  
transaction type: <builtin: TPC-B (sort of)>  
scaling factor: 1  
query mode: extended  
number of clients: 50  
number of threads: 2  
duration: 30 s  
number of transactions actually processed: 14043  
latency average = 104.572 ms  
latency stddev = 78.161 ms  
average connection time = 2.263 ms  
tps = 466.951766 (including reconnection times)  

## Выводы ##
### 1. Значения параметров, рекомендованные pgtune, немного ускорили выполнение легкого теста ###
### 2. При отключении synchronous_commit быстродействие увеличивается значительно: ###
### 2.1. Для легкого теста с 868 tps до 2410 tps. ###
### 2.2. Для более тяжелого теста с 352 tps до 466 tps. ###



