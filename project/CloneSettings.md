## Настройки после клонирования ВМ ##

### Настройка общей папки ВМ ###
Настройки -> Общие папки -> Изменить  
Путь к папке, например: C:\Users\Admin\VirtualBox VMs\Ubuntu02\shared

### Установка порта ###
$ sudo -i -u postgres  
$ sudo nano /var/lib/postgresql/14/main/postgresql.auto.conf   
Добавить строку:
port = 5433
Ctrl + S
Ctrl + X

$ sudo service postgresql restart
$ psql
postgres=# show port;
\q

### Определение IP-адреса ###
$ ip address  
Например: 192.168.1.7  