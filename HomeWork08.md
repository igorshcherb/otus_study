>создать GCE инстанс типа e2-medium и диском 10GB

>установить на него PostgreSQL 14 с дефолтными настройками

>применить параметры настройки PostgreSQL из прикрепленного к материалам занятия файла

>выполнить pgbench -i postgres

>запустить pgbench -c8 -P 60 -T 600 -U postgres postgres

>дать отработать до конца

>дальше настроить autovacuum максимально эффективно

>построить график по получившимся значениям

>так чтобы получить максимально ровное значение tps


**pgbench -i postgres**

**pgbench -i postgres**

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


**postgres@adm1-VirtualBox:~$ pgbench -c8 -P 60 -T 600 -U postgres postgres**

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


