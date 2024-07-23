# Ansible Automation Project

### Introduction

A Jump Server (also known as a Bastion Host) is an intermediary server that provides access to the internal network. Ideally, web servers should be inside a secured network, inaccessible directly from the Internet. This setup means DevOps engineers cannot SSH into the web servers directly but must access them through a Jump Server, enhancing security and reducing the attack surface.

In the diagram below, the Virtual Private Cloud (VPC) is divided into two subnets: the Public Subnet with Public IP Addresses and the Private Subnet, only reachable by Private IP addresses.

![Network Architecture](Images/1.png)

This guide details how to install and configure an Ansible Client to act as a Jump Server/Bastion Host and create a simple Ansible Playbook for server configuration automation.

### Prerequisites

1. NFS Server
2. Web Server 1
3. Web Server 2
4. Load Balancer
5. Database Server
6. Jenkins Server

### Steps to Install and Configure Ansible Client as a Jump Server/Bastion Host and Create a Simple Ansible Playbook

## Step 1: Install and Configure Ansible on an EC2 Instance

1. **Create a new repository** called `ansible-config-mgt` in your GitHub account.

![Create Repository](Images/2..png)

2. **Update the Name tag** on your Jenkins EC2 Instance to `Jenkins-Ansible`. This server will be used to run playbooks.

3. **Install Ansible** on the `Jenkins-Ansible` server.
    ```bash
    sudo apt update && sudo apt install ansible -y
    ```

4. **Check the Ansible version** running on your instance:
    ```bash
    ansible --version
    ```

![Check Ansible Version](Images/4.png)

## Step 2: Configure Jenkins Build Job

1. **Log into Jenkins**:
    ```bash
    http://public_ip_jenkins_ansible_instance:8080
    ```

2. **Create a new Freestyle Job** called `ansible`, select "discard old builds," set a maximum of 2 builds to keep, and point it to your `ansible-config-mgt` repository.

![Configure Jenkins Job](Images/5.png)
![Job Configuration](Images/6.png)
![Job Configuration](Images/7.png)
![Job Configuration](Images/8.png)
![Job Configuration](Images/9.png)
![Job Configuration](Images/10.png)

3. **Select GitHub hook trigger for GITScm polling** and configure a Post-Build Job to archive the artifacts.

![Post-Build Job Configuration](Images/11.png)
![Post-Build Job Configuration](Images/12.png)

## Step 3: Prepare Your Development Environment

1. **Download and install Visual Studio Code (VS Code)**.

2. **Configure VS Code** to connect to your GitHub repository.

3. **Clone the `ansible-config-mgt` repository** to your local machine:
    ```bash
    git clone <ansible-config-mgt-repository-link>
    ```

![Clone Repository](Images/13.png)

4. **Navigate to the `ansible-config-mgt` directory** and pull the latest changes:
    ```bash
    cd ansible-config-mgt && git pull
    ```

![Pull Repository](Images/14.png)

## Step 4: Set Up Ansible Inventory

1. **Create a new branch** for development:
    ```bash
    git checkout -b prj-145
    ```

![Create Branch](Images/15.png)

2. **Create `playbooks` and `inventory` directories**:
    ```bash
    mkdir playbooks inventory
    ```

![Create Directories](Images/16.png)

3. **Create your first playbook** named `common.yml` in the `playbooks` directory:
    ```bash
    cd playbooks && touch common.yml
    ```

![Create Playbook](Images/17.png)

4. **Create inventory files** for each environment in the `inventory` directory:
    ```bash
    cd .. && cd inventory && touch dev staging uat prod
    ```

![Create Inventory Files](Images/18.png)

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate.

5. **Add SSH keys to the SSH agent**:
    ```bash
    eval ssh-agent -s
    ssh-add <path-to-private-key>
    ```

![Add SSH Keys](Images/19.png)

6. **SSH into the `Ansible-jenkins` server** using SSH agent:
    ```bash
    ssh -A ubuntu@public-ip
    ```

![SSH into Server](Images/20.png)

7. **Update your `inventory/dev.yml` file**:
    ```bash
    [nfs]
    <NFS-Server-Private-IP-Address> ansible_ssh_user='ec2-user'

    [webservers]
    <Web-Server1-Private-IP-Address> ansible_ssh_user='ec2-user'
    <Web-Server2-Private-IP-Address> ansible_ssh_user='ec2-user'

    [db]
    <Database-Private-IP-Address> ansible_ssh_user='ec2-user' 

    [lb]
    <Load-Balancer-Private-IP-Address> ansible_ssh_user='ubuntu'
    ```

![Inventory Configuration](Images/21.png)

## Step 5: Create a Common Playbook

1. **Update your `playbooks/common.yml` file**:
    ```yaml
    ---
    - name: Common configuration for RHEL servers
      hosts: nfs, webservers, db
      become: yes
      tasks:
        - name: Install wireshark
          yum:
            name: wireshark
            state: latest

    - name: Common configuration for Ubuntu server
      hosts: lb
      become: yes
      tasks:
        - name: Update apt cache
          apt:
            update_cache: yes

        - name: Install wireshark
          apt:
            name: wireshark
            state: latest
    ```

![Common Playbook](Images/22.png)

The code has two plays: the first for RHEL servers (NFS, Database, Web) and the second for the Ubuntu server (Load Balancer).

## Step 6: Update GIT with the Latest Code

1. **Check the status of your branch**, add files, commit changes, and push your branch to GitHub:
    ```bash
    git status
    git add .
    git commit -m "Add common playbook and inventory files"
    git push origin prj-145
    ```

2. **Create a Pull Request** on GitHub:
    - Go to your `ansible-config-mgt` repository.
    - Click on "Compare & pull request".
    - Click on "Create pull request".
    - Click on "Merge pull request" and confirm.

![Create Pull Request](Images/23.png)
![Create Pull Request](Images/24.png)
![Merge Pull Request](Images/25.png)

3. **Checkout from `prj-145` branch to `main`** and pull the latest changes:
    ```bash
    git checkout main && git pull
    ```

![Pull Main Branch](Images/26.png)

Jenkins will be triggered to run the Ansible job and save all build artifacts.

## Step 7: Run the First Ansible Test

1. **SSH into the `Jenkins-Ansible` server**:
    ```bash
    ssh ubuntu@public_ip_address_of_jenkins_ansible
    ```

![SSH into Server](Images/27.png)

2. **Navigate to the build artifacts**:
    ```bash
    cd /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/ && ll
    ```

![Navigate to Artifacts](Images/28.png)

3. **Run the Ansible Playbook**:
    ```bash
    ansible-playbook -i inventory/dev playbooks/common.yml
    ```

![Run Playbook](Images/29.png)

4. **Verify Wireshark installation** using Ansible Adhoc commands:
    ```bash
    ansible webservers -i inventory/dev -m command -a "wireshark --version"
    ansible nfs,db -i inventory/dev -m command -a "wireshark --version"
    ansible lb -i inventory/dev -m command -a "wireshark --version"
    ```

![Verify Installation](Images/30.png)
![Verify Installation](Images/31.png)

### Final Setup

![Final Setup](Images/32.png)
