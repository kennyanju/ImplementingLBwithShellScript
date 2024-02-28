# implement wordpress with lvm storage management

the task is to prepare storage infrastructure on 2 linux servers and implement a basic web solution using wordpress

## web solution with wordpress

the tasks are:

- configure a storage subsystem for linux web and database servers

- install wordpress and connect it to a remote mysql database server

### prepare a web sever

launch an ec2 instance and create 3 volumes of 10GB

![alt text](<Screenshot 2024-02-20 at 16.35.38.png>)
![alt text](<Screenshot 2024-02-20 at 16.36.04.png>)
![alt text](<Screenshot 2024-02-20 at 16.39.57.png>)
![alt text](<Screenshot 2024-02-20 at 16.45.39.png>)

```bash
lsblk
df -h
sudo gdisk /dev/xvdf
sudo yum install lvm2
sudo lvmdiskscan
```

![alt text](<Screenshot 2024-02-20 at 16.48.11.png>)
![alt text](<Screenshot 2024-02-20 at 16.51.10.png>)
![alt text](<Screenshot 2024-02-20 at 16.58.19.png>)

#### use pvcreate ultility to mark each of 3 disks

```bash
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
sudo pvs
```

#### use vgcreate ultitlity to add all 3 pvs to a volume group

```bash
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
sudo vgs
```

#### use lvcreate ultility to create logical volumes

```bash
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
sudo lvs
```

#### verify setup

```bash
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk 
```

#### use mkfs.ext4 to format logical volumes

```bash
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

#### create directory to store webfiles, and back up log data

```bash
sudo mkfs -t ext4 /dev/webdata-vg/apps-lv
sudo mkfs -t ext4 /dev/webdata-vg/logs-lv
```

### mount logical volume

```bash
sudo mount /dev/webdata-vg/apps-lv /var/www/html/
```

### back up all files

```bash
sudo rsync -av /var/log/. /home/recovery/logs/
```

### mount logical volume /var/log

```bash
sudo mount /dev/webdata-vg/logs-lv /var/log
```

### restore log files into /var/log

```bash
sudo rsync -av /home/recovery/logs/log/. /var/log
```

### update /etc/fstab

```bash
sudo blkid
sudo vi /etc/fstab
update /etc/fstab
```

### test the configuration

```bash
sudo mount -a
sudo systemctl daemon-reload
```

### verify setup is running

```bash
df -h
```

## install and configure mysql database

### install wordpress on webserver ec2

```bash
sudo yum -y update
```

### install wget, apache

```bash
sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json
```

### start apache

```bash
sudo systemctl enable httpd
sudo systemctl start httpd
```

### install PHP and its dependencies

```bash
sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
sudo yum module list php
sudo yum module reset php
sudo yum module enable php:remi-7.4
sudo yum install php php-opcache php-gd php-curl php-mysqlnd
sudo systemctl start php-fpm
sudo systemctl enable php-fpm
setsebool -P httpd_execmem 1
```

### restart apache

```bash
sudo systemctl restart httpd
```

### download and copy wordpress

```bash
mkdir wordpress
cd   wordpress
sudo wget http://wordpress.org/latest.tar.gz
sudo tar xzvf latest.tar.gz
sudo rm -rf latest.tar.gz
cp wordpress/wp-config-sample.php wordpress/wp-config.php
cp -R wordpress /var/www/html/
```

### configure SELinux policies

```bash
 sudo chown -R apache:apache /var/www/html/wordpress
 sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
 sudo setsebool -P httpd_can_network_connect=1
```

## install mysql on EC2 DB server

```bash
sudo yum update
sudo yum install mysql-server
```

### verify service is running

```bash
sudo systemctl restart mysqld
sudo systemctl enable mysqld
```

### configure DB to work on wordpress

```bash
sudo mysql
CREATE DATABASE wordpress;
CREATE USER `myuser`@`<Web-Server-Private-IP-Address>` IDENTIFIED BY 'mypass';
GRANT ALL ON wordpress.* TO 'myuser'@'<Web-Server-Private-IP-Address>';
FLUSH PRIVILEGES;
SHOW DATABASES;
exit
```

### install mysql client

```bash
sudo yum install mysql
sudo mysql -u admin -p -h <DB-Server-Private-IP-address>
```

### visit web link