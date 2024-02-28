# NFS Server Setup on Amazon EC2 with RHEL 8 and LVM Configuration

This tutorial guides you through the process of setting up an NFS (Network File System) server on an Amazon EC2 instance running RHEL (Red Hat Enterprise Linux) 8, using Logical Volume Manager (LVM) with XFS file systems. This setup is designed to serve web applications, storing applications on `/mnt/apps`, logs on `/mnt/logs`, and options on `/mnt/opt`.

## Prerequisites

- An AWS account.
- Basic knowledge of AWS EC2, RHEL, and terminal usage.

## Step 1: Launch EC2 Instance

1. Sign in to the AWS Management Console.
2. Navigate to EC2 and click "Launch Instance".
3. Choose "Red Hat Enterprise Linux 8" as the Amazon Machine Image (AMI).
4. Select an instance type, then click "Next: Configure Instance Details".
5. Configure instance details as required, ensuring your instance is within the desired subnet.
6. Add storage if needed, then click "Next: Add Tags".
7. Add relevant tags, then click "Next: Configure Security Group".
8. Create a new security group allowing SSH access.
9. Review and launch the instance.

## Step 2: Configure LVM with XFS File Systems

1. Connect to your EC2 instance via SSH.
2. Install LVM2 package if it's not already installed:

   ```bash
   sudo yum install lvm2 -y
   ```


3. Check available disks with `lsblk` and create physical volumes (replace `/dev/xvdf` with your disk):

   ```bash
   sudo pvcreate /dev/xvdf
   ```


4. Create a volume group named `vg-web`:

   ```bash
   sudo vgcreate vg-web /dev/xvdf
   ```


5. Create logical volumes:

   ```bash
   sudo lvcreate -n lv-apps -L 10G vg-web
   sudo lvcreate -n lv-logs -L 5G vg-web
   sudo lvcreate -n lv-opt -L 5G vg-web
   ```


6. Format the logical volumes with XFS:

   ```bash
   sudo mkfs.xfs /dev/vg-web/lv-apps
   sudo mkfs.xfs /dev/vg-web/lv-logs
   sudo mkfs.xfs /dev/vg-web/lv-opt
   ```


7. Create mount points and mount the volumes:

   ```bash
   sudo mkdir /mnt/apps /mnt/logs /mnt/opt
   sudo mount /dev/vg-web/lv-apps /mnt/apps
   sudo mount /dev/vg-web/lv-logs /mnt/logs
   sudo mount /dev/vg-web/lv-opt /mnt/opt
   ```

   
8. To ensure mounts persist after reboot, add them to `/etc/fstab`.

```bash
sudo nano /etc/fstab
```

```bash
/dev/vg-web/lv-apps /mnt/apps xfs defaults 0 2
/dev/vg-web/lv-logs /mnt/logs xfs defaults 0 2
/dev/vg-web/lv-opt /mnt/opt xfs defaults 0 2
```

```bash
sudo mount -a
```

## Step 3: Install and Configure NFS Server

1. Install NFS server package:

   ```bash
   sudo yum install nfs-utils -y
   ```


2. Enable and start NFS service:

   ```bash
   sudo systemctl enable --now nfs-server
   ```


3. Export the mounts for NFS clients (edit `/etc/exports` and add):

   ```
   /mnt/apps <subnet-cidr>(rw,sync,no_root_squash)
   /mnt/logs <subnet-cidr>(rw,sync,no_root_squash)
   /mnt/opt <subnet-cidr>(rw,sync,no_root_squash)
   ```


   Replace `<subnet-cidr>` with your subnet CIDR.

4. Apply the export settings:

   ```bash
   sudo exportfs -arv
   ```


5. Open NFS port in the security group:
   - Go to EC2 Dashboard > Security Groups.
   - Edit inbound rules of your instance’s security group to allow TCP port 2049 (default NFS port).

## Step 4: Test NFS Access from a Client

1. On a client within the same subnet, install `nfs-utils`.
2. Mount the NFS share:

   ```bash
   sudo mount -t nfs <nfs-server-ip>:/mnt/apps /local/mountpoint
   ```

   
3. Verify the mount and test read/write operations.

## Conclusion

You now have an NFS server set up with LVM on RHEL 8, serving applications, logs, and options over the network. Ensure to adjust security settings according to your organization's policies for production environments.