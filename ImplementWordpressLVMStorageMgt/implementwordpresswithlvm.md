# implement wordpress with lvm storage management

## web solution with wordpress

### prepare a web sever
```bash
lsblk
df -h
sudo gdisk /dev/xvdf
sudo yum install lvm2
sudo lvmdiskscan
```

```bash
sudo pvcreate /dev/xvdf1
sudo pvcreate /dev/xvdg1
sudo pvcreate /dev/xvdh1
sudo pvs
```

```bash
sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1
sudo vgs
```

```bash
sudo lvcreate -n apps-lv -L 14G webdata-vg
sudo lvcreate -n logs-lv -L 14G webdata-vg
sudo lvs
```

```bash
sudo vgdisplay -v #view complete setup - VG, PV, and LV
sudo lsblk 
```



###