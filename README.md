# Домашнее задание к занятию "Репликация и масштабирование. Часть 1" - Пергунов Д.В

### Задание 1



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
7.Указываем в контейнере SLAVE данный MASTER сервера:
```sql
CHANGE MASTER TO  
    MASTER_HOST='replication-master',  
    MASTER_USER='replication',  
    MASTER_PASSWORD='password',  
    MASTER_LOG_FILE='mysql-bin.000001',  
    MASTER_LOG_POS=1540;
```
8.Выводим конфигурацию с помощью команды SHOW SLAVE STATUS\G;  
![image](https://github.com/dimindrol/Replicationscaling.P1-pergunov/assets/103885836/12a4d6b4-4d5c-4884-a813-d9d5409424d1)  


