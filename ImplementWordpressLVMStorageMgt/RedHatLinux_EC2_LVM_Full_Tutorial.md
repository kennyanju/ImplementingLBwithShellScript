
# Setting Up a Web Server on Red Hat Linux with LVM on Amazon EC2

This tutorial will guide you through the process of setting up a web server on Red Hat Linux running on an Amazon EC2 instance. You will learn how to create three volumes, attach them to your EC2 instance, and manage these volumes using the Logical Volume Manager (LVM).

## Prerequisites

- An AWS account
- Basic knowledge of Linux commands
- An EC2 instance running Red Hat Linux

## Step 1: Prepare Your Web Server

1. Connect to your EC2 instance using SSH.
2. Update your system packages:

```bash
sudo yum update -y
```

![alt text](<Screenshot 2024-02-28 at 09.38.39.png>)

3. Install the Apache web server:

```bash
sudo yum install httpd -y
```

![alt text](<Screenshot 2024-02-28 at 09.39.31.png>)

4. Start the Apache server:

```bash
sudo systemctl start httpd
```

5. Enable Apache to start at boot:

```bash
sudo systemctl enable httpd
```

![alt text](<Screenshot 2024-02-28 at 09.41.00.png>)

## Step 2: Create and Attach Volumes

1. Go to the EC2 Management Console.
2. Navigate to Volumes and create three new volumes of 10GB each in the same availability zone as your EC2 instance.
3. Attach each volume to your EC2 instance using the console.

![alt text](<Screenshot 2024-02-28 at 09.42.54.png>)

## Step 3: Inspect Block Devices

1. Use `lsblk` to list the block devices:

```bash
lsblk
```
![alt text](<Screenshot 2024-02-28 at 09.44.32.png>)

## Step 4: Partition the Disks

1. Use `fdisk` to create a single partition on each of the 3 disks:

```bash
sudo fdisk /dev/xvdX
```

Replace `/dev/xvdX` with the actual device name for each disk.

![alt text](<Screenshot 2024-02-28 at 09.47.01.png>)

## Step 5: Install LVM Tools

1. Install LVM tools:

```bash
sudo yum install lvm2 -y
```

![alt text](<Screenshot 2024-02-28 at 09.47.57.png>)

2. Use `lvmdiskscan` to check for available partitions:

```bash
sudo lvmdiskscan
```
![alt text](<Screenshot 2024-02-28 at 09.49.06.png>)

## Step 6: Create Physical Volumes

1. Mark each disk as a physical volume:

```bash
sudo pvcreate /dev/xvdX1
```

Replace `/dev/xvdX1` with the partition name for each disk.

![alt text](<Screenshot 2024-02-28 at 09.50.10.png>)

2. Verify the physical volumes:

```bash
sudo pvs
```
![alt text](<Screenshot 2024-02-28 at 09.51.02.png>)

## Step 7: Create a Volume Group

1. Add all physical volumes to a volume group named `web-data-vg`:

```bash
sudo vgcreate web-data-vg /dev/xvdX1 /dev/xvdY1 /dev/xvdZ1
```

Replace `/dev/xvdX1`, `/dev/xvdY1`, and `/dev/xvdZ1` with your actual partition names.

![alt text](<Screenshot 2024-02-28 at 09.52.18.png>)

2. Verify the volume group:

```bash
sudo vgs
```

![alt text](<Screenshot 2024-02-28 at 09.53.10.png>)

## Step 8: Create Logical Volumes

1. Create the `apps-lv` logical volume using half of the PV size:

```bash
sudo lvcreate -n apps-lv -L 15G web-data-vg
```

2. Create the `logs-lv` logical volume using the remaining space:

```bash
sudo lvcreate -n logs-lv -l 100%FREE web-data-vg
```

3. Verify the logical volumes:

```bash
sudo lvs
```

![alt text](<Screenshot 2024-02-28 at 09.54.41.png>)

## Step 9: Verify the Entire Setup

1. Display the complete setup:

```bash
sudo vgdisplay -v
```

![alt text](<Screenshot 2024-02-28 at 09.55.27.png>)

2. List the block devices to verify the setup:

```bash
sudo lsblk
```

![alt text](<Screenshot 2024-02-28 at 09.56.12.png>)

## Step 10: Format Logical Volumes

1. Format the `apps-lv` and `logs-lv` logical volumes with the ext4 filesystem:

```bash
sudo mkfs.ext4 /dev/web-data-vg/apps-lv
sudo mkfs.ext4 /dev/web-data-vg/logs-lv
```

![alt text](<Screenshot 2024-02-28 at 10.08.18.png>)

## Step 11: Create Directories for Web and Log Data

1. Create a directory for storing website files:

```bash
sudo mkdir -p /var/www/html
```

2. Create a directory for storing backup log data:

```bash
sudo mkdir -p /home/recovery/logs
```

## Step 12: Mount Logical Volumes

1. Mount the `apps-lv` logical volume to `/var/www/html`:

```bash
sudo mount /dev/web-data-vg/apps-lv /var/www/html
```

2. Mount the `logs-lv` logical volume to `/var/log`:

```bash
sudo mount /dev/web-data-vg/logs-lv /var/log
```

![alt text](<Screenshot 2024-02-28 at 10.09.39.png>)

## Step 13: Backup and Restore Log Files

1. Use the `rsync` utility to backup all files in the `/var/log` directory to `/home/recovery/logs`:

```bash
sudo rsync -av /var/log/ /home/recovery/logs/
```

2. After mounting the `logs-lv` logical volume to `/var/log`, restore the log files:

```bash
sudo rsync -av /home/recovery/logs/ /var/log/
```

![alt text](<Screenshot 2024-02-28 at 10.10.56.png>)

## Step 14: Update fstab for Persistent Mounting

1. Edit `/etc/fstab` to add entries for the logical volumes to ensure they are mounted at boot:

```bash
echo '/dev/web-data-vg/apps-lv /var/www/html ext4 defaults 0 0' | sudo tee -a /etc/fstab
echo '/dev/web-data-vg/logs-lv /var/log ext4 defaults 0 0' | sudo tee -a /etc/fstab
```

![alt text](<Screenshot 2024-02-28 at 10.11.56.png>)

## Step 15: Test Configuration and Reload Daemon

1. Test the `fstab` file to ensure there are no errors:

```bash
sudo mount -a
```

2. Reload the daemon to apply changes:

```bash
sudo systemctl daemon-reload
```

![alt text](<Screenshot 2024-02-28 at 10.12.55.png>)

## Step 16: Verify Your Setup

1. Verify the mounts and filesystems are correctly configured:

```bash
df -h
```

2. Check that the logical volumes are mounted as expected:

```bash
lsblk
```

![alt text](<Screenshot 2024-02-28 at 10.13.39.png>)

## Step 17: Launch a Second EC2 Instance for the DB Server

1. Launch a second Red Hat EC2 instance from the AWS Management Console.
2. Label this instance as 'DB Server' for clarity.

## Step 18: Prepare the DB Server

After launching your second Red Hat EC2 instance intended for the DB Server role, follow these steps to prepare it for use. This process will mirror the preparation of the Web Server with a focus on creating a volume for database storage.

1. Connect to your DB Server EC2 instance using SSH.

2. Update your system packages:

```bash
sudo yum update -y
```

3. Install the required tools for managing logical volumes:

```bash
sudo yum install lvm2 -y
```

4. Assuming you've attached three new volumes to this instance as well, inspect the block devices:

```bash
lsblk
```

5. Use `fdisk` or `parted` to create a single partition on each of the 3 disks. For example, to partition the first disk:

```bash
sudo fdisk /dev/xvdX
```

Replace `/dev/xvdX` with the actual device name for each disk.

6. Mark each disk as a physical volume for use by LVM:

```bash
sudo pvcreate /dev/xvdX1
```

Replace `/dev/xvdX1` with the partition name for each disk.

7. Create a volume group named `db-data-vg` using all three physical volumes:

```bash
sudo vgcreate db-data-vg /dev/xvdX1 /dev/xvdY1 /dev/xvdZ1
```

Replace `/dev/xvdX1`, `/dev/xvdY1`, and `/dev/xvdZ1` with your actual partition names.

8. Create a logical volume named `db-lv` intended for database storage. You can allocate the entire volume group space to this logical volume:

```bash
sudo lvcreate -n db-lv -l 100%VG db-data-vg
```

9. Format the `db-lv` logical volume with the ext4 file system:

```bash
sudo mkfs.ext4 /dev/db-data-vg/db-lv
```

10. Create a mount point for the database files:

```bash
sudo mkdir /db
```

11. Mount the `db-lv` logical volume to the `/db` directory:

```bash
sudo mount /dev/db-data-vg/db-lv /db
```

12. To ensure the mount persists across reboots, add it to the `/etc/fstab` file:

```bash
echo '/dev/db-data-vg/db-lv /db ext4 defaults 0 0' | sudo tee -a /etc/fstab
```

13. Test the `fstab` entry and reload the daemon:

```bash
sudo mount -a
sudo systemctl daemon-reload
```

14. Verify the setup by checking the mounted volumes:

```bash
df -h
```

At this point, your DB Server is prepared with a dedicated logical volume mounted to `/db`, ready for database installation and configuration.
Repeat the steps from the tutorial to prepare the DB Server instance. Instead of creating `apps-lv`, create `db-lv` and mount it to the `/db` directory.

## Step 19: Install WordPress on the Web Server

1. Update the repository:

```bash
sudo yum update -y
```

2. Install `wget`, Apache, and its dependencies:

```bash
sudo yum install wget httpd -y
```

3. Start Apache:

```bash
sudo systemctl start httpd
sudo systemctl enable httpd
```

4. Install PHP and its dependencies:

```bash
sudo yum install php php-mysql php-gd php-pear -y
```

5. Restart Apache to apply PHP installation:

```bash
sudo systemctl restart httpd
```

6. Download WordPress and prepare the directory:

```bash
wget https://wordpress.org/latest.tar.gz
tar -xzvf latest.tar.gz
sudo rsync -av wordpress/ /var/www/html/
```

7. Configure SELinux Policies:

```bash
sudo chcon -Rt httpd_sys_rw_content_t /var/www/html/
sudo setsebool -P httpd_can_network_connect 1
```

## Step 20: Install MySQL on the DB Server EC2

1. Install MySQL:

```bash
sudo yum install mysql-server -y
sudo systemctl start mysqld
sudo systemctl enable mysqld
```

## Step 21: Configure DB to Work with WordPress

1. Secure MySQL installation:

```bash
sudo mysql_secure_installation
```

2. Create a WordPress database and user:

```bash
mysql -u root -p
CREATE DATABASE wordpress_db;
CREATE USER 'wordpress_user'@'%' IDENTIFIED BY 'password';
GRANT ALL PRIVILEGES ON wordpress_db.* TO 'wordpress_user'@'%';
FLUSH PRIVILEGES;
EXIT;
```

## Step 22: Configure WordPress to Connect to the Remote Database

1. Edit the WordPress configuration file on the Web Server:

```bash
cd /var/www/html
cp wp-config-sample.php wp-config.php
vim wp-config.php
```

2. Update the database configuration settings with the DB Server details:

```php
define('DB_NAME', 'wordpress_db');
define('DB_USER', 'wordpress_user');
define('DB_PASSWORD', 'password');
define('DB_HOST', 'DB_Server_IP_Address');
```

Replace `DB_Server_IP_Address` with the actual IP address of your DB Server EC2 instance.

Congratulations! You have successfully installed and configured WordPress on your Web Server EC2 instance to connect to a MySQL database on a separate DB Server EC2 instance.