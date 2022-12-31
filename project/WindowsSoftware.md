## Установка программного обеспечения в хостовую операциолнную систему (Windows 11) ##

### Установка VirtualBox ###
Сайты дистрибутивов VirtualBox:  
* https://www.virtualbox.org/wiki/Downloads  
* https://www.oracle.com/cis/virtualization/technologies/vm/downloads/virtualbox-downloads.html  

### Установка DBeaver ###
Сайт дистрибутива DBeaver:  
https://dbeaver.io/download/ 

Пример настройки соединения с БД:  
Название соединения: postgres_regsys2  
Хост: 192.168.1.9  
Порт: 5433  
База данных: postgres  
Пользователь: postgres  
Пароль: <пароль>  

### Установка Pentaho Data Integration ###
Сайт дистрибутива PDI:  
https://sourceforge.net/projects/pentaho/files/latest/download  

Файл дистрибутива: pdi-ce-9.3.0.0-428.zip  

Страница java 8 на сайте Oracle:  
https://www.oracle.com/java/technologies/downloads/#java8  
Файл дистрибутива java 8: jre-8u351-windows-x64.exe  

Добавить переменные окружения Windows:  
* PENTAHO_JAVA_HOME = C:\Program Files\Java\jre1.8.0_351  
* PENTAHO_JAVA = C:\Program Files\Java\jre1.8.0_351  

Пример настройки соединения с БД:  
Connection name: regsys2  
Connection type: PostgreSQL  
Access: Native (JDBC)  
Host Name: 192.168.1.9   
Database Name: postgres  
Port Number: 5433  
User: postgres  
Password: <пароль>  


 
