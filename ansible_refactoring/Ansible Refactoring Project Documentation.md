# Ansible Refactoring Project Documentation

### Objective
The goal of this project is to improve the code arrangement, readability, and scalability of Project 11. Additionally, we aim to create a path to save all artifacts to prevent the Jenkins server from becoming congested.

## Step 1: Setting up the Server

1. **Using the Existing Ansible-Jenkins Server**:
   - Continue with the `ansible-jenkins` server from Project 11, which already has Ansible and Jenkins installed.

2. **Create a Directory for Artifacts**:
   - Create a folder called `ansible-config-artifact` in the home directory to save all artifacts and set permissions for the `ubuntu` user:
     ```bash
     sudo mkdir ansible-config-artifact
     sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact
     ```
   ![Create Directory](Images/1.png)

3. **Install the "Copy Artifacts" Plugin in Jenkins**:
   - Log into the Jenkins web console and install the "Copy Artifacts" plugin:
     - Navigate to `Manage Jenkins` > `Plugins` > `Search for "Copy Artifacts"` and install without restarting Jenkins.
   ![Install Plugin](Images/2.png)
   ![Install Plugin](Images/3.png)

4. **Configure Jenkins Projects**:
   - Use the existing `ansible` project in Jenkins as the upstream project to trigger a downstream project.
   - Create a new Freestyle project called `save_artifacts` to serve as the downstream project. Move this project to the home directory of `ubuntu`:
     ```bash
     mv /var/lib/jenkins/workspace/save_artifacts /home/ubuntu/
     ```
   - Configure the `save_artifacts` project:
   ![Configure Project](Images/4.png)
   ![Configure Project](Images/5.png)
   ![Configure Project](Images/6.png)
   ![Configure Project](Images/7.png)
   ![Configure Project](Images/8.png)
   ![Configure Project](Images/9.png)

## Step 2: Refactoring by Importing Other Playbooks

1. **Create a New Branch for Refactoring**:
   - Create a new branch called `refactor` for all changes before merging to the main branch:
     ```bash
     git checkout -b refactor
     ```

2. **Create and Update `site.yml`**:
   - Create a `site.yml` file in the playbooks directory:
     ```bash
     touch playbooks/site.yml
     ```

3. **Organize Playbooks**:
   - Create a `static-assignments` folder and move `common.yml` from Project 11 into this folder:
     ```bash
     sudo mkdir static-assignments
     sudo mv playbooks/common.yml static-assignments/
     ```

4. **Update `site.yml` to Import `common.yml`**:
   - Edit `site.yml` to import `common.yml`:
     ```yaml
     - import_playbook: static-assignments/common.yml
     ```
   ![Update site.yml](Images/10.png)
   ![Update site.yml](Images/11.png)
   ![Update site.yml](Images/12.png)

5. **Create `common-del.yml`**:
   - Create a `common-del.yml` file in `static-assignments` to delete Wireshark installed in Project 11:
     ```bash
     touch static-assignments/common-del.yml
     ```

6. **Update `site.yml` with `common-del.yml`**:
   - Update `site.yml` to include `common-del.yml` instead of `common.yml`:
     ```yaml
     - import_playbook: static-assignments/common-del.yml
     ```
   ![Create common-del.yml](Images/13.png)
   ![Create common-del.yml](Images/14.png)
   ![Create common-del.yml](Images/15.png)

7. **Commit and Push Changes**:
   - Add, commit, and push changes to the `refactor` branch:
     ```bash
     git add .
     git commit -m "Refactor playbooks structure"
     git push origin refactor
     ```
   - Create a pull request, merge it into the main branch, and pull the changes on the server:
     ```bash
     git checkout main
     git pull
     ```

## Step 3: Configuring UAT Web Servers with a Role

1. **Update Inventory**:
   - Use the private IPs of the existing UAT servers from Project 11 and update `inventory/uat.yml`.

2. **Set Up Roles**:
   - Create a `roles` directory and use `ansible-galaxy` to initialize a `webserver` role:
     ```bash
     mkdir roles
     cd roles
     ansible-galaxy init webserver
     ```

3. **Configure Ansible**:
   - Edit `/etc/ansible/ansible.cfg` to state the path of the roles inventory and disable `ssh_key_checking`:
     ```ini
     [defaults]
     roles_path = /path/to/roles
     host_key_checking = False
     ```

4. **Create Tasks for `webserver` Role**:
   - Edit `roles/webserver/tasks/main.yml` to install Apache and deploy the tooling application:
     ```yaml
     ---
     - name: Install Apache
       apt:
         name: apache2
         state: present

     - name: Clone Tooling Repository
       git:
         repo: 'https://github.com/your-username/tooling'
         dest: /var/www/html/tooling
     ```
   ![Configure Role](Images/16.png)
   ![Configure Role](Images/17.png)
   ![Configure Role](Images/18.png)
   ![Configure Role](Images/19.png)
   ![Configure Role](Images/20.png)
   ![Configure Role](Images/21.png)
   ![Configure Role](Images/22.png)

5. **Run Playbooks**:
   - Commit and push changes, then run the playbook on the server to ensure successful execution:
     ```bash
     git add .
     git commit -m "Configure UAT webservers with webserver role"
     git push origin refactor
     ```
   ![Run Playbook](Images/23.png)
   ![Run Playbook](Images/24.png)

## Step 4: Confirm Tooling Interface

- Verify the tooling interface by navigating to the public IP addresses of the UAT servers and accessing `/web/index.php`:
   ![Verify Tooling Interface](Images/25.png)
   ![Verify Tooling Interface](Images/26.png)
