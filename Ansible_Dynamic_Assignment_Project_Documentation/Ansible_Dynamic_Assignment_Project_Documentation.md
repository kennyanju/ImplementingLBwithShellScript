# Ansible Dynamic Assignment Project Documentation

### Objective
This project aims to implement dynamic configuration using the `include` module. It builds on Project 11 and 12. While Project 12 explored static assignments using the `import` module, this project utilizes the `include` module, which is the key difference between static and dynamic assignments.

**Static Assignment (`import`)**:
- Processes all referenced playbooks at the time of parsing.
- Does not account for changes during execution.

**Dynamic Assignment (`include`)**:
- Processes statements during playbook execution.
- Accounts for changes encountered during execution.

**Note**:
- Static assignments are generally more reliable and stable and are recommended for most use cases.
- Dynamic assignments are useful for environment-specific variables.

## Step 1: Creating Dynamic Assignment Structure

1. **Create a Branch and Folder for Dynamic Assignments**:
   - Create a new branch called `dynamic-assignment` and a folder called `dynamic-assignments` with a file named `env-vars.yml`.

   ![Create Folder](Images/1.png)
   ![Create File](Images/2.png)

2. **Update `site.yml`**:
   - Update the `site.yml` file to include the dynamic assignments.

   ![Update site.yml](Images/3.png)

3. **Install Necessary Roles from Ansible Galaxy**:
   - Use the Ansible Galaxy community to install required roles (e.g., MySQL).
   - After editing the roles, commit the changes to GitHub, merge the branch into the main branch, pull to the production server, and run the playbook against the inventory.

   ![Install Roles](Images/4.png)
   ![Commit Changes](Images/5.png)
   ![Merge Branch](Images/6.png)

## Step 2: Setting Up Environment Variables

1. **Define Environment Variables**:
   - In the `dynamic-assignments` folder, define environment-specific variables in `env-vars.yml`.

   ![Define Variables](Images/7.png)

2. **Integrate Environment Variables in Playbooks**:
   - Ensure that the `env-vars.yml` file is properly included in the playbooks.

   ![Include Variables](Images/8.png)

## Step 3: Configuring Nginx and Apache Load Balancers

1. **Set New Configuration**:
   - Create configuration settings in `defaults/main.yml`.

   ![Configure Defaults](Images/9.png)

2. **Create Configuration Files for Nginx and Apache**:
   - In the `static-assignments` folder, create files for Apache and Nginx load balancers.

   ![Create Apache File](Images/10.png)
   ![Create Nginx File](Images/11.png)

3. **Run the Playbook to Install and Configure Load Balancers**:
   - Run the playbook to install and configure Nginx and Apache as load balancers.

   ![Run Playbook](Images/12.png)
   ![Install Nginx](Images/13.png)
   ![Configure Nginx](Images/14.png)
   ![Install Apache](Images/15.png)
   ![Configure Apache](Images/16.png)

## Step 4: Testing and Verification

1. **Test Configuration**:
   - Verify that the load balancers and other configurations are correctly set up.

   ![Test Configuration](Images/17.png)

2. **Check for Errors**:
   - Ensure there are no errors and configurations are applied as expected.

   ![Check Errors](Images/18.png)

3. **Confirm Setup**:
   - Confirm that the environment is functioning correctly with the new dynamic assignments.

   ![Confirm Setup](Images/19.png)

## Conclusion

By following these steps, you have successfully implemented dynamic assignments using the `include` module in Ansible, allowing for environment-specific variables and configurations. This setup enhances the flexibility and scalability of your Ansible automation.

Thank you!
