## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 08. MVCC, vacuum и autovacuum.  ##  
# Домашнее задание #  


>создать GCE инстанс типа e2-medium и диском 10GB

>установить на него PostgreSQL 14 с дефолтными настройками

>применить параметры настройки PostgreSQL из прикрепленного к материалам занятия файла

>выполнить pgbench -i postgres

>запустить pgbench -c8 -P 60 -T 600 -U postgres postgres

>дать отработать до конца

>дальше настроить autovacuum максимально эффективно

>построить график по получившимся значениям

>так чтобы получить максимально ровное значение tps


**sudo -i -u postgres**

**pgbench -i postgres -p 5433**

**dropping old tables...**

**NOTICE:  table "pgbench_accounts" does not exist, skipping**

**NOTICE:  table "pgbench_branches" does not exist, skipping**

**NOTICE:  table "pgbench_history" does not exist, skipping**

**NOTICE:  table "pgbench_tellers" does not exist, skipping**

**creating tables...**

**generating data (client-side)...**

**100000 of 100000 tuples (100%) done (elapsed 0.07 s, remaining 0.00 s)**

**vacuuming...**

**creating primary keys...**

**done in 0.25 s (drop tables 0.01 s, create tables 0.02 s, client-side generate 0.12 s, vacuum 0.06 s, primary keys 0.04 s).**

### Запустил pgbench на сервере с настройками по умолчанию ###


**postgres@adm1-VirtualBox:~$ pgbench -c8 -P 60 -T 600 -p 5433 -U postgres postgres**

**pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))**

**starting vacuum...end.**

**progress: 60.0 s, 934.2 tps, lat 8.521 ms stddev 7.676**

**progress: 120.0 s, 937.9 tps, lat 8.492 ms stddev 7.688**

**progress: 180.0 s, 933.7 tps, lat 8.528 ms stddev 7.581**

**progress: 240.0 s, 925.6 tps, lat 8.604 ms stddev 7.581**

**progress: 300.0 s, 916.8 tps, lat 8.687 ms stddev 7.969**

**progress: 360.0 s, 930.9 tps, lat 8.555 ms stddev 7.688**

**progress: 420.0 s, 931.5 tps, lat 8.553 ms stddev 7.804**

**progress: 480.0 s, 930.5 tps, lat 8.557 ms stddev 7.816**

**progress: 540.0 s, 922.6 tps, lat 8.634 ms stddev 7.974**

**progress: 600.0 s, 930.3 tps, lat 8.560 ms stddev 7.859**

**transaction type: <builtin: TPC-B (sort of)>**

**scaling factor: 1**

**query mode: simple**

**number of clients: 8**

**number of threads: 1**

**duration: 600 s**

**number of transactions actually processed: 557641**

**latency average = 8.569 ms**

**latency stddev = 7.765 ms**

**initial connection time = 11.532 ms**

**tps = 929.395574 (without initial connection time)**

### Сделал рекомендованные настройки PostgreSQL ###

**max_connections = 40**

**shared_buffers = 1GB**

**effective_cache_size = 3GB**

**maintenance_work_mem = 512MB**

**checkpoint_completion_target = 0.9**

**wal_buffers = 16MB**

**default_statistics_target = 500**

**random_page_cost = 4**

**effective_io_concurrency = 2**

**work_mem = 6553kB**

**min_wal_size = 4GB**

**max_wal_size = 16GB**

### Запустил pgbench на сервере с рекомендованными настройками ###

**pgbench -c8 -P 60 -T 600 -p 5433 -U postgres postgres**

**pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))**

**starting vacuum...end.**

**progress: 60.0 s, 926.9 tps, lat 8.590 ms stddev 7.748**

**progress: 120.0 s, 930.0 tps, lat 8.562 ms stddev 7.737**

**progress: 180.0 s, 937.6 tps, lat 8.495 ms stddev 7.663**

**progress: 240.0 s, 896.6 tps, lat 8.884 ms stddev 8.058**

**progress: 300.0 s, 909.6 tps, lat 8.757 ms stddev 7.969**

**progress: 360.0 s, 870.7 tps, lat 9.151 ms stddev 9.767**

**progress: 420.0 s, 852.8 tps, lat 9.338 ms stddev 10.366**

**progress: 480.0 s, 890.7 tps, lat 8.947 ms stddev 8.329**

**progress: 540.0 s, 913.5 tps, lat 8.713 ms stddev 7.745**

**progress: 600.0 s, 690.5 tps, lat 11.558 ms stddev 11.087**

**transaction type: <builtin: TPC-B (sort of)>**

**scaling factor: 1**

**query mode: simple**

**number of clients: 8**

**number of threads: 1**

**duration: 600 s**

**number of transactions actually processed: 529140**

**latency average = 9.033 ms**

**latency stddev = 8.676 ms**

**initial connection time = 9.062 ms**

**tps = 881.872580 (without initial connection time)**

### Вывод 1: установка рекомендованных настроек ухудшила показатели производительности PostgreSQL ###

### Установил настройки autovacuum ("мягкие" настройки) ###

**autovacuum_work_mem = -1**

**autovacuum = on**

**autovacuum_max_workers = 1**

**autovacuum_naptime = 1min**

**autovacuum_vacuum_threshold = 50**

**autovacuum_vacuum_insert_threshold = 1000**

**autovacuum_analyze_threshold = 50**

**autovacuum_vacuum_scale_factor = 0.2**

**autovacuum_vacuum_insert_scale_factor = 0.2**

**autovacuum_analyze_scale_factor = 0.1**

**autovacuum_freeze_max_age = 200000000

**autovacuum_multixact_freeze_max_age = 400000000**

**autovacuum_vacuum_cost_delay = 2ms**

**autovacuum_vacuum_cost_limit = -1**

### Запустил pgbench с включенным autovacuum ###

**pgbench -c8 -P 60 -T 600 -p 5433 -U postgres postgres**

**pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))**

**starting vacuum...end.**

**progress: 60.0 s, 928.1 tps, lat 8.577 ms stddev 7.756**

**progress: 120.0 s, 927.9 tps, lat 8.581 ms stddev 7.657**

**progress: 180.0 s, 925.3 tps, lat 8.606 ms stddev 7.859**

**progress: 240.0 s, 909.0 tps, lat 8.763 ms stddev 7.839**

**progress: 300.0 s, 889.7 tps, lat 8.955 ms stddev 8.369**

**progress: 360.0 s, 922.4 tps, lat 8.634 ms stddev 7.786**

**progress: 420.0 s, 916.7 tps, lat 8.689 ms stddev 7.740**

**progress: 480.0 s, 919.6 tps, lat 8.662 ms stddev 7.852**

**progress: 540.0 s, 920.8 tps, lat 8.651 ms stddev 7.705**

**progress: 600.0 s, 915.9 tps, lat 8.697 ms stddev 7.914**

**transaction type: <builtin: TPC-B (sort of)>**

**scaling factor: 1**

**query mode: simple**

**number of clients: 8**

**number of threads: 1**

**duration: 600 s**

**number of transactions actually processed: 550530**

**latency average = 8.680 ms**

**latency stddev = 7.849 ms**

**initial connection time = 10.814 ms**

**tps = 917.540383 (without initial connection time)**

### Вывод 2: "мягкий" autovacuum почти не снижает производительность сервера ###

### Сделал настройки autovacuum более "жесткими" ###

**autovacuum_max_workers = 3**

**autovacuum_naptime = 10s**

**autovacuum_vacuum_insert_threshold = 10**

**autovacuum_analyze_threshold = 10**

**autovacuum_vacuum_scale_factor = 0.01**

**autovacuum_analyze_scale_factor = 0.005**

### Запустил pgbench с "жесткими" настройками autovacuum ###

**pgbench -c8 -P 60 -T 600 -p 5433 -U postgres postgres**

**pgbench (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))**

**starting vacuum...end.**

**progress: 60.0 s, 919.7 tps, lat 8.654 ms stddev 7.530**

**progress: 120.0 s, 923.0 tps, lat 8.627 ms stddev 7.579**

**progress: 180.0 s, 920.9 tps, lat 8.646 ms stddev 7.545**

**progress: 240.0 s, 911.7 tps, lat 8.736 ms stddev 7.649**

**progress: 300.0 s, 908.1 tps, lat 8.768 ms stddev 7.839**

**progress: 360.0 s, 916.2 tps, lat 8.692 ms stddev 7.954**

**progress: 420.0 s, 919.5 tps, lat 8.657 ms stddev 7.621**

**progress: 480.0 s, 914.8 tps, lat 8.705 ms stddev 7.794**

**progress: 540.0 s, 915.3 tps, lat 8.700 ms stddev 7.840**

**progress: 600.0 s, 914.1 tps, lat 8.711 ms stddev 7.669**

**transaction type: <builtin: TPC-B (sort of)>**

**scaling factor: 1**

**query mode: simple**

**number of clients: 8**

**number of threads: 1**

**duration: 600 s**

**number of transactions actually processed: 549810**

**latency average = 8.689 ms**

**latency stddev = 7.703 ms**

**initial connection time = 12.626 ms**

**tps = 916.342294 (without initial connection time)**

### Вывод 3: "жесткий" autovacuum немного снижает производительность сервера ###

