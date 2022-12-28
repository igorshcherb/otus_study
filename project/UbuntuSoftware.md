## Установка программного обеспечения в гостевую операциолнную систему (Ubuntu) ##

### Установка Midnight Commander ###
* добавить репозиторий universe:  
$ sudo add-apt-repository universe  
* установить mc:  
$ sudo apt install mc  

### Установка DBeaver ###
* установить java  
$ sudo apt update  
$ sudo apt -y install default-jdk  
$ java -version  
* добавить репозиторий DBeaver  
$ sudo snap install curl  # version 7.86.0  
$ curl -fsSL https://dbeaver.io/debs/dbeaver.gpg.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/dbeaver.gpg  
$ echo "deb https://dbeaver.io/debs/dbeaver-ce /" | sudo tee /etc/apt/sources.list.d/dbeaver.list  
* обновить список apt и установить DBeaver  
$ sudo apt update  
$ sudo apt install dbeaver-ce  