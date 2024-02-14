# implement wordpress with lvm storage management

the task is to prepare storage infrastructure on 2 linux servers and implement a basic web solution using wordpress

## web solution with wordpress

the tasks are:

- configure a storage subsystem for linux web and database servers 

- install wordpress and connect it to a remote mysql database server

### prepare a web sever

launch an ec2 instance and create 3 volumes of 10GB 

```bash
lsblk
df -h
sudo gdisk /dev/xvdf
sudo yum install lvm2
sudo lvmdiskscan
```

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

