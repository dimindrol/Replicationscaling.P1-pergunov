# Домашнее задание к занятию "Репликация и масштабирование. Часть 1" - Пергунов Д.В

### Задание 1
Master-Slave репликация:  
Master - главный сервер, который умеет записывать данные и читать их.  
Slave - сервер репликации, необходим для репликации данных полученных от сервера Мастер.  
Данные в схеме Master-Slave, передаются в одном направлении от мастера к Slave.  
Запись данных может выполняться только на мастер сервер, а чтение данных может выполняться как с Master, так и со Slave.  

Master-Master репликация:  
В данной схеме все серверы равны, и оба сервера способны как записывать данные, так и читать их.  
Главный недостаток данной схемы, это риск конфликтов между серверами, но при этом данный тип репликации позволяет распределить нагрузку на запись.  
Данный способ более гибкий, но более сложен в настройке.  

### Задание 2
1.Запустили два Докер контейнера:
![image](https://github.com/dimindrol/Replicationscaling.P1-pergunov/assets/103885836/8c866b80-eaa2-41f1-9aaa-dfaf23babac6)  
2.Создали сеть репликация и добавили туда наши докер контейнеры:  
![image](https://github.com/dimindrol/Replicationscaling.P1-pergunov/assets/103885836/44b1ce1a-36d1-4da7-b5c8-bbe7724d9be6)  
3.Заходим в контейнеры и создаем пользователя 
```sql
CREATE USER 'replication'@'%' IDENTIFIED BY 'password';
GRANT REPLICATION SLAVE, REPLICATION CLIENT ON *.* TO 'replication'@'%';
FLUSH PRIVILEGES;
```
4.Меняем настройки у контейнера мастера по пути /etc/my.cnf  
```
server_id = 1  
log_bin = mysql-bin
```
5.Использовали команду SHOW MASTER STATUS;  
![image](https://github.com/dimindrol/Replicationscaling.P1-pergunov/assets/103885836/76f1a4e1-ea9f-45f5-8865-03e49c6f2b9c)  
6.Настраиваем контейнер SLAVE и перезагружаем контейнер  
```
log_bin = mysql-bin  
server_id = 2  
relay-log = /var/lib/mysql/mysql-relay-bin  
relay-log-index = /var/lib/mysql/mysql-relay-bin.index  
read_only = 1
```
7.Указываем в контейнере SLAVE данные MASTER сервера:
```sql
CHANGE MASTER TO  
    MASTER_HOST='replication-master',  
    MASTER_USER='replication',  
    MASTER_PASSWORD='password',  
    MASTER_LOG_FILE='mysql-bin.000001',  
    MASTER_LOG_POS=1540;
```
8.Выводим конфигурацию репликации с помощью команды SHOW SLAVE STATUS\G;  
Replica has read all, говорит нам о том, что репликация работает.  
![image](https://github.com/dimindrol/Replicationscaling.P1-pergunov/assets/103885836/12a4d6b4-4d5c-4884-a813-d9d5409424d1)  


