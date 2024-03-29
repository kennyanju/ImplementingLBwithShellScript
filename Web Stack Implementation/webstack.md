# Creating a webstack  with nginx

### Launch a EC2 instance and connect to it

 ![Alt text](<Screenshot 2024-02-07 at 13.18.50.png>)

### Update the instance

 ```bash
sudo apt update
sudo apt install nginx
```
![Alt text](<Screenshot 2024-02-07 at 13.29.47.png>)

### check if nginx is running

 ```bash
sudo systemctl status nginx
```
![Alt text](<Screenshot 2024-02-07 at 13.32.03.png>)

### Visit URL to see nginx installtion

![Alt text](<Screenshot 2024-02-07 at 13.41.57.png>)

## install mysql server
```bash
sudo apt install mysql-server
```
![Alt text](<Screenshot 2024-02-07 at 15.55.31.png>)

### open mysql
```bash
sudo mysql
```
![Alt text](<Screenshot 2024-02-07 at 15.58.12.png>)

### make your deployment more secure
```bash
sudo mysql-p
```
![Alt text](<Screenshot 2024-02-09 at 09.49.57.png>)

## install php
```bash
sudo apt install php-fpm php-mysql
```
![Alt text](<Screenshot 2024-02-09 at 09.54.09.png>)
 
## Configure nginx to use php

### create script file
```bash
sudo mkdir /var/www/projectLEMP
$ sudo chown -R $USER:$USER /var/www/projectLEMP
```
![Alt text](<Screenshot 2024-02-09 at 09.58.03.png>)

### use nano to write script
```bash
sudo nano /etc/nginx/sites-available/projectLEMP
```
![Alt text](<Screenshot 2024-02-09 at 09.59.37.png>)

### run and test script
```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
sudo nginx -t
```
![Alt text](<Screenshot 2024-02-09 at 10.02.36.png>)

### Disable default nginx host and reload nginx
```bash
sudo unlink /etc/nginx/sites-enabled/default
sudo systemctl reload nginx
```
![Alt text](<Screenshot 2024-02-09 at 10.07.23.png>)

### Create a test html page and view ypur deployment
```bash
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html

```
![Alt text](<Screenshot 2024-02-09 at 10.10.06.png>)

### test php with nginx
```bash
nano /var/www/projectLEMP/info.php
```
![Alt text](<Screenshot 2024-02-09 at 10.12.48.png>)

![Alt text](<Screenshot 2024-02-09 at 10.13.59.png>)

## retrieve data from Mysql dtabase with PHP
We will  create new database with a sample todo list

### create new database
```bash
sudo mysql
mysql> CREATE DATABASE `example_database`;
```

![Alt text](<Screenshot 2024-02-12 at 09.25.13.png>)

### show databases and create table
```bash
SHOW DATABASES
CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
```
![Alt text](<Screenshot 2024-02-12 at 09.30.27.png>)


### create new user and grant permissions
![Alt text](<Screenshot 2024-02-12 at 09.45.49.png>)

### insert rows and confirm data
```bash
CREATE TABLE example_database.todo_list (item_id INT AUTO_INCREMENT,content VARCHAR(255),PRIMARY KEY(item_id));
INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
```
![Alt text](<Screenshot 2024-02-12 at 09.33.34.png>)
![Alt text](<Screenshot 2024-02-12 at 09.36.18.png>)

### connect mysql to php
![Alt text](<Screenshot 2024-02-12 at 09.38.12.png>)
![Alt text](<Screenshot 2024-02-12 at 09.45.06.png>)