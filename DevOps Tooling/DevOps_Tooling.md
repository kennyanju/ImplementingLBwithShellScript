
# Setting up the NFS Server

This guide covers the steps required to set up an NFS (Network File System) server on a Red Hat Enterprise Linux (RHEL) 8.6 instance in AWS. The NFS server will serve as a centralized storage solution for your DevOps tooling website, allowing multiple web servers to access shared files.

## Prerequisites

- An AWS account with permissions to create EC2 instances and EBS volumes.
- Basic knowledge of Linux commands.

## Steps

### 1. Provision NFS Server

- Launch an EC2 instance using RHEL 8.6 as the operating system. Name this instance `nfs-server`.

### 2. Create and Attach Volumes

- Create three EBS volumes in the same availability zone as your `nfs-server`.
- Attach these volumes to your `nfs-server` instance. They will appear as `/dev/nvme1n1`, `/dev/nvme2n1`, and `/dev/nvme3n1`.

### 3. SSH and Disk Configuration

- SSH into your `nfs-server`.
- Use the `gdisk` utility to partition each of the three disks.

  ```bash
  sudo gdisk /dev/nvme1n1
  sudo gdisk /dev/nvme2n1
  sudo gdisk /dev/nvme3n1
  ```

### 4. Install LVM2

- Install the `lvm2` package.

  ```bash
  sudo yum install lvm2 -y
  ```

### 5. Configure LVM

- Create physical volumes on each partition.

  ```bash
  sudo pvcreate /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
  ```

- Create a volume group named `webdata-vg`.

  ```bash
  sudo vgcreate webdata-vg /dev/nvme1n1p1 /dev/nvme2n1p1 /dev/nvme3n1p1
  ```

- Create logical volumes for apps, logs, and opt.

  ```bash
  sudo lvcreate -n lv-apps -L 9G webdata-vg
  sudo lvcreate -n lv-logs -L 9G webdata-vg
  sudo lvcreate -n lv-opt -L 9G webdata-vg
  ```

### 6. Format and Mount Volumes

- Format the logical volumes with XFS and mount them.

  ```bash
  sudo mkfs -t xfs /dev/webdata-vg/lv-apps
  sudo mkfs -t xfs /dev/webdata-vg/lv-logs
  sudo mkfs -t xfs /dev/webdata-vg/lv-opt
  
  sudo mkdir /mnt/apps /mnt/logs /mnt/opt
  
  sudo mount /dev/webdata-vg/lv-apps /mnt/apps
  sudo mount /dev/webdata-vg/lv-logs /mnt/logs
  sudo mount /dev/webdata-vg/lv-opt /mnt/opt
  ```

### 7. Install and Configure NFS

- Install `nfs-utils` and start the NFS server.

  ```bash
  sudo yum install nfs-utils -y
  sudo systemctl start nfs-server.service
  sudo systemctl enable nfs-server.service
  ```

- Configure NFS exports.

  ```bash
  sudo vi /etc/exports
  
  # Add the following lines, replace the CIDR with your network's CIDR
  /mnt/apps <your-network-CIDR>(rw,sync,no_all_squash,no_root_squash)
  /mnt/logs <your-network-CIDR>(rw,sync,no_all_squash,no_root_squash)
  /mnt/opt <your-network-CIDR>(rw,sync,no_all_squash,no_root_squash)
  ```

- Apply and restart NFS server.

  ```bash
  sudo exportfs -arv
  sudo systemctl restart nfs-server.service
  ```

## Conclusion

You have now successfully set up an NFS server with additional storage configured for your DevOps tooling website. This server will provide shared storage for your web servers to host applications, logs, and other necessary data.


# Configuring the Database Server

This guide outlines the steps to configure a MySQL database server for your DevOps tooling website. The database server will be used to store data for the tooling website, such as user information and tooling records.

## Prerequisites 2

- An AWS account with permissions to create EC2 instances.
- A running NFS server configured as per the previous guide.
- Basic knowledge of MySQL.

## Steps 2

### 1. Launch Database Server

- Launch an EC2 instance using Ubuntu 20.04 LTS as the operating system. This instance will serve as your database server.

### 2. SSH into Database Server

- SSH into your newly created database server instance.

### 3. Install MySQL Server

- Update your package lists and install the MySQL server package.

  ```bash
  sudo apt update
  sudo apt install mysql-server -y
  ```

- Secure your MySQL installation.

  ```bash
  sudo mysql_secure_installation
  ```

### 4. Configure MySQL

- Log into the MySQL shell as root.

  ```bash
  sudo mysql -u root -p
  ```

- Create a new database named `tooling`.

  ```sql
  CREATE DATABASE tooling;
  ```

- Create a new user and grant permissions to the `tooling` database.

  ```sql
  CREATE USER 'webaccess'@'%' IDENTIFIED BY 'password';
  GRANT ALL PRIVILEGES ON tooling.* TO 'webaccess'@'%';
  FLUSH PRIVILEGES;
  ```

- Update MySQL's bind address to allow connections from any IP address.

  Edit the MySQL configuration file:

  ```bash
  sudo vi /etc/mysql/mysql.conf.d/mysqld.cnf
  ```

  Find the line `bind-address = 127.0.0.1` and change it to `bind-address = 0.0.0.0`.

- Restart the MySQL service to apply changes.

  ```bash
  sudo systemctl restart mysql
  ```

## Conclusion 2

Your database server is now configured and ready to store data for your DevOps tooling website. You have created a `tooling` database and a user with the necessary permissions to access and manage the database from any web server within your infrastructure.


# Preparing the Web Server

This guide demonstrates how to prepare a web server for hosting the DevOps tooling website. It covers the installation of a web server (Apache), PHP, and necessary configurations to integrate with NFS and the database server.

## Prerequisites 3

- An AWS account with permissions to create EC2 instances.
- A configured NFS server and a database server as per previous guides.
- Basic knowledge of web server configuration.

## Steps 3

### 1. Launch Web Server

- Launch an EC2 instance using RHEL 8.6 as the operating system. This instance will act as your web server.

### 2. SSH into Web Server

- SSH into your newly created web server instance.

### 3. Install NFS Client

- Install NFS utilities to allow your web server to connect to the NFS server.

  ```bash
  sudo yum install nfs-utils nfs4-acl-tools -y
  ```

### 4. Mount NFS Shares

- Create a directory to mount the NFS shares from the NFS server.

  ```bash
  sudo mkdir /var/www
  sudo mount -t nfs -o rw,nosuid <NFS-Server-IP>:/mnt/apps /var/www
  ```

  Replace `<NFS-Server-IP>` with the private IP address of your NFS server.

- Ensure the mount persists across reboots by adding it to `/etc/fstab`.

  ```bash
  sudo vi /etc/fstab
  ```

  Add the following line:

  ```
  <NFS-Server-IP>:/mnt/apps /var/www nfs defaults 0 0
  ```

### 5. Install and Configure Apache and PHP

- Install Apache web server.

  ```bash
  sudo yum install httpd -y
  ```

- Start and enable Apache to run on boot.

  ```bash
  sudo systemctl start httpd
  sudo systemctl enable httpd
  ```

- Install PHP and necessary extensions.

  ```bash
  sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
  sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm
  sudo dnf module reset php
  sudo dnf module enable php:remi-7.4
  sudo dnf install php php-opcache php-gd php-curl php-mysqlnd
  ```

- Start and enable PHP-FPM.

  ```bash
  sudo systemctl start php-fpm
  sudo systemctl enable php-fpm
  ```

- Adjust SELinux policies if necessary.

  ```bash
  sudo setsebool -P httpd_execmem 1
  ```

### 6. Final Steps

- Test your web server by creating a simple `test.txt` file in `/var/www/html` and accessing it via your web server's IP address.
- Configure Apache to use the NFS-mounted directories for its web content and logs.
- Install Git, clone your project's repository into `/var/www/html`, and configure your website.

## Conclusion 3

Your web server is now configured to serve the DevOps tooling website, with content stored on a centralized NFS server and a database backend ready for dynamic content management. You've installed and configured Apache, PHP, and integrated with your NFS and database servers to support your website's operation.


# Deploying the Tooling Website

This final guide covers the steps to deploy the DevOps tooling website on your configured web server, including cloning the website's code from a Git repository, configuring the website to connect to your database, and verifying the deployment.

## Prerequisites 4

- A web server prepared as per the previous guide.
- Access to the Git repository containing the tooling website's code.
- A configured database server ready to store the website's data.

## Steps 4

### 1. Clone the Website's Code

- Install Git on your web server.

  ```bash
  sudo yum install git -y
  ```

- Clone the repository containing the tooling website's code into the `/var/www/html` directory. Replace `<repository-url>` with the actual URL of your Git repository.

  ```bash
  cd /var/www/html
  git clone <repository-url> .
  ```

### 2. Configure Database Connection

- Navigate to the location where you've cloned your website's code.
- Update the database configuration file with your database server details (host, database name, username, and password). This file is typically named `config.php`, `db.php`, or similar.

### 3. Apply Database Schema

- Inside the cloned directory, locate the SQL script provided to initialize your database schema (e.g., `tooling-db.sql`).
- Log into your MySQL database server and apply the SQL script.

  ```bash
  mysql -u username -p database_name < tooling-db.sql
  ```

  Replace `username`, `database_name`, and `tooling-db.sql` with your database user, the name of your database, and the path to the SQL script, respectively.

### 4. Adjust Permissions and SELinux Policies

- Ensure the web server user (typically `apache` or `www-data`) has the necessary permissions to read and write to the website's directories and files.
- Adjust SELinux policies if you're using a Red Hat-based distribution to allow the web server to interact with the NFS-mounted filesystem and the database.

  ```bash
  sudo setenforce 0
  sudo chcon -R -t httpd_sys_content_t /var/www/html/
  sudo chcon -R -t httpd_sys_rw_content_t /var/www/html/writable_directory
  ```

### 5. Restart Web Server

- After configuring everything, restart your web server to apply all changes.

  ```bash
  sudo systemctl restart httpd
  ```

### 6. Verify the Deployment

- Open a web browser and navigate to your web server's public IP address. You should see the landing page of the tooling website, indicating that the deployment is successful.

## Conclusion 4

You have successfully deployed the DevOps tooling website on your web server. The website is now accessible and configured to interact with your NFS server for file storage and your database server for dynamic content management.

