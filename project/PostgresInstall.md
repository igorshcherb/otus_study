## Установка PostgreSQL в гостевую операционную систему (Ubuntu) ##

### Установка PostgreSQL ###
$ sudo apt update  
$ sudo apt install postgresql postgresql-contrib  
$ sudo systemctl start postgresql.service  

### Проверка установки PostgreSQL, смена паролей ###
$ sudo passwd postgres  
$ sudo -i -u postgres  
$ pg_lsclusters  
$ psql  
postgres=# select * from version();  
alter user postgres with password 'p';  
postgres=# \q  
$ exit  

### Добавление пользователя postgres в группу sudo ###
$ sudo adduser postgres sudo  
