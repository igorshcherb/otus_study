## PostgreSQL для администраторов баз данных и разработчиков ##  
## Занятие 06. Физический уровень PostgreSQL ##  
# Домашнее задание #  


**создал виртуальную машину c Ubuntu 20.04 LTS**

**поставил на нее PostgreSQL 14 через sudo apt**

>проверьте что кластер запущен через sudo -u postgres pg_lsclusters

**sudo -u postgres pg_lsclusters**

*Ver Cluster Port Status Owner    Data directory              Log file*

*14  main    5432 online postgres /var/lib/postgresql/14/main /var/log/postgresql/postgresql-14-main.log*

>зайдите из под пользователя postgres в psql и сделайте произвольную таблицу с произвольным содержимым

**postgres1=# create table test(c1 text);**

**postgres1=# insert into test values('1');**

**\q**

**создал новый диск в ВМ размером 10GB, добавил его к виртуальной машине**

![Ubuntu1_new_disk](https://github.com/igorshcherb/otus_study/raw/main/Ubuntu1_new_disk)

**инициализировал и отформатировал диск**

**подмонтировал диск**

**sudo mkdir /mnt/sdb** 

**sudo mount /dev/sdb /mnt/sdb** 

**sudo nano /etc/fstab**

**/dev/sdb    /mnt/sdb     ext4      defaults        0             0** 

>перезагрузите инстанс и убедитесь, что диск остается примонтированным

**sudo mount | grep sdb**

**/dev/sdb on /mnt/sdb type ext4 (rw,relatime)**

>сделайте пользователя postgres владельцем /mnt/data

**sudo chown -R postgres:postgres /mnt/sdb**

**sudo -u postgres pg_ctlcluster 14 main stop**

*Warning: stopping the cluster using pg_ctlcluster will mark the systemd unit as failed. Consider using systemctl:*

*sudo systemctl stop postgresql@14-main*

**sudo systemctl stop postgresql@14-main**

>перенесите содержимое /var/lib/postgres/14 в /mnt/data - mv /var/lib/postgresql/14 /mnt/data

**sudo mv /var/lib/postgresql/14 /mnt/sdb**

>попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 14 main start
>напишите получилось или нет и почему

*Error: /var/lib/postgresql/14/main is not accessible or does not exist*

>задание: найти конфигурационный параметр в файлах раположенных в /etc/postgresql/14/main который надо поменять и поменяйте его
>напишите что и почему поменяли

**sudo nano /etc/postgresql/14/main/postgresql.conf**

**было:**

**data_directory = '/var/lib/postgresql/14/main'**

**стало:**

**data_directory = '/mnt/sdb/14/main'**

>попытайтесь запустить кластер - sudo -u postgres pg_ctlcluster 14 main start
>напишите получилось или нет и почему

**sudo -u postgres pg_ctlcluster 14 main start**

*Warning: the cluster will not be running as a systemd service. Consider using systemctl:*

*sudo systemctl start postgresql@14-main*

**sudo systemctl start postgresql@14-main**

*Job for postgresql@14-main.service failed because the service did not take the steps required by its unit configuration.*  
*See "systemctl status postgresql@14-main.service" and "journalctl -xeu postgresql@14-main.service" for details.*  

*postgresql@14-main.service - PostgreSQL Cluster 14-main*  
*Loaded: loaded (/lib/systemd/system/postgresql@.service; enabled-runtime; vendor preset: enabled)*  
*Active: failed (Result: protocol) since Sun 2022-11-06 10:32:16 MSK; 58s ago*  
*Process: 2510 ExecStart=/usr/bin/pg_ctlcluster --skip-systemctl-redirect 14-main start (code=exited, status=2)*  
*CPU: 21ms*

*ноя 06 10:32:16 adm1-VirtualBox systemd[1]: Starting PostgreSQL Cluster 14-main...*  
*ноя 06 10:32:16 adm1-VirtualBox postgresql@14-main[2510]: Cluster is already running.*  
*ноя 06 10:32:16 adm1-VirtualBox systemd[1]: postgresql@14-main.service: New main PID 2484 does not belong to service, and PID file is not owned by r>*  
*ноя 06 10:32:16 adm1-VirtualBox systemd[1]: postgresql@14-main.service: New main PID 2484 does not belong to service, and PID file is not owned by r>*  
*ноя 06 10:32:16 adm1-VirtualBox systemd[1]: postgresql@14-main.service: Failed with result 'protocol'.*  
*ноя 06 10:32:16 adm1-VirtualBox systemd[1]: Failed to start PostgreSQL Cluster 14-main.*  

**после перезапуска Ubuntu проблема пропала**


>зайдите через через psql и проверьте содержимое ранее созданной таблицы

**psql**

*psql (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))*

*Type "help" for help.*

**postgres1=# select * from test;**

*c1*

*----*

*1*

*(1 row)*

>задание со звездочкой *: 
>не удаляя существующий инстанс ВМ сделайте новый, 
>поставьте на его PostgreSQL, 
>удалите файлы с данными из /var/lib/postgres, 
>перемонтируйте внешний диск который сделали ранее от первой виртуальной машины ко второй 
>и запустите PostgreSQL на второй машине так чтобы он работал 
>с данными на внешнем диске, расскажите как вы это сделали и что в итоге получилось.

**проделал указанные действия**

**не перезапускал кластер после редактирования /etc/postgresql/14/main/postgresql.conf, сразу перезапустил Ubuntu**

**получил результат:**

**psql**

*psql (14.5 (Ubuntu 14.5-0ubuntu0.22.04.1))*

*Type "help" for help.*

**postgres1=# select * from test;**

*c1*

*----*

*1*

*(1 row)*

