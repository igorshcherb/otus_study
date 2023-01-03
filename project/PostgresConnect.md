## Подключение ПО хостовой ОС к PostgreSQL ##

### Настройка ВМ ###
Настройки -> Сеть -> Тип подключения: Сетевой мост  

Запустить ВМ.  

### Настройка listener-а ###
$ sudo -i -u postgres  
$ sudo nano /var/lib/postgresql/14/main/postgresql.auto.conf   
Добавить строку:  
listen_addresses = '*'  
Ctrl+S  
Ctrl+X  

### Настройка pg_hba.conf ###
$ sudo nano /etc/postgresql/14/main/pg_hba.conf  
Добавить строку:  
host	all	all	0.0.0.0/0	trust  
Ctrl+S   
Ctrl+X  

### Перезапуск PostgreSQL ###
$ sudo service postgresql restart  
$ psql  
postgres=# show listen_addresses;  
\q  

### Определение IP-адреса ###
$ ip address  
Например: 192.168.1.7  

### Настройка DBeaver в хостовой ОС ###
Соединение: postgres_regsys1  
Host: 192.168.1.7  
Port: 5432  
Database: postgres  
Username: postgres  
Password: <password>  

Соединение: postgres_regsys2 
Host: 192.168.1.9  
Port: 5433  
Database: postgres  
Username: postgres  
Password: <password>  


 
