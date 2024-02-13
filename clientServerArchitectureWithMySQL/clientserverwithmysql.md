# Implement a client server architecutre using mysql DBMS
this task is to implement a client server architecture so that i can access a database on a server from another server

## create two ec2 instances
sudo apt install mysql-server

![alt text](<Screenshot 2024-02-13 at 12.52.18.png>)

### install mysql server on ec2 instance named mysql sever
```bash

```
![alt text](<Screenshot 2024-02-13 at 12.56.42.png>)

### install mysqlclient on sever named mysql client
```bash
sudo apt install mysql-client
```
![alt text](<Screenshot 2024-02-13 at 13.05.37.png>)

### adjust security groups tp allow port 3306

![alt text](<Screenshot 2024-02-13 at 20.37.56.png>)

### configure mysqlserver to allow connections from remote hosts
```bash
CREATE USER 'new_user'@'35.178.123.85' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON *.* TO 'new_user'@'35.178.123.85' WITH GRANT OPTION;
FLUSH PRIVILEGES;
```
![alt text](<Screenshot 2024-02-13 at 13.04.01.png>)

### connect to mysql server from my sql client and show databases
```bash
sudo sql
Show databases;
```

![alt text](<Screenshot 2024-02-13 at 20.37.08.png>)
