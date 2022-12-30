## Установка и настройка PostgreSQL в Docker-е ##

### Установка и настройка Docker Desktop ###

Скачать дистрибутив Docker Desktop с сайта:  
https://www.docker.com/  

Кнопка [download docker desktop].  

Запустить дистрибутив DockerDesktopInstaller.exe.  

Поставить галочку "Use WSL 2 instead of Hyper-V (recommended)".  

Скачать и установить wsl_update_x64.msi, когда будет предложено.  


### Варианты запуска PostgreSQL в Docker-е ###

1. Воспользоваться кнопкой PostgreSQL [Run] в интерфейсе Docker Desktop.  

Соединение с PostgreSQL:  
postgres://postgres:postgrespw@localhost:49153  

2. Запуск PostgreSQL через терминал Docker-а:  
docker pull postgres  
docker run -itd -e POSTGRES_USER=postgres -e POSTGRES_PASSWORD=<пароль> -p 5435:5435 -v /data:/var/lib/postgresql/data --name postgresql postgres  
docker run --name postgres -p 5435:5435 -e POSTGRES_PASSWORD=<пароль> -d postgres  
psql --username=postgres --dbname=postgres  

В этом случае для соединения с PostgreSQL используются порт и пароль, заданные в командной строке.  
