# Ansible Refactoring & Static Assignments: Imports and Roles

This guide outlines the steps to improve Jenkins job configured in this project [https://github.com/Rational-Being/Devops-Trainig-with-STEGHUB/tree/main/Ansible_Configuration_Management] for managing Ansible artifacts more efficiently. By refactoring the Jenkins pipeline, we avoid redundant directories and conserve storage space by centralizing artifact storage.
## Why Refactoring?

Refactoring plays an essential role in DevOps by improving efficiency and maintainability; refactoring improves scalability and readability by organizing tasks into logical, reusable sections. By breaking down tasks into separate files or roles, you create a more manageable and scalable structure, which is also easier for others to follow and maintain. This refined setup supports the collaborative, iterative improvement central to the DevOps philosophy and ensures your playbook is ready to scale with future infrastructure needs.

## Step 1: Jenkins Job Enhancement

We start by enhancing the Jenkins job configuration to address the current issues:

- **Current Problem**: Each code change creates a new directory on the Jenkins server, leading to clutter and unnecessary storage consumption.
- **Solution**: Create a new Jenkins job that consolidates artifacts in a dedicated directory using the Copy Artifact plugin.

### Setting Up the Artifact Storage Directory

1. **Create the Artifact Directory**:
   Log into your Jenkins-Ansible server and create a central directory for storing build artifacts.

   ```bash
   sudo mkdir /home/ubuntu/ansible-config-artifact
   ```

2. **Adjust Directory Permissions**:
   Change the permissions to allow Jenkins to save files here:

   ```bash
   sudo chmod -R 0777 /home/ubuntu/ansible-config-artifact
   ```

### Installing the Copy Artifact Plugin

1. In the Jenkins web console, go to **Manage Jenkins** > **Manage Plugins**.
2. Under the **Available** tab, search for **Copy Artifact**.
3. Install the plugin without requiring a Jenkins restart.

### Creating the `save_artifacts` Job

Next, create a Freestyle project named `save_artifacts` to store artifacts from each build in one central location.

1. In Jenkins, create a new **Freestyle project** and name it `save_artifacts`.
2. Set the project to trigger upon the successful completion of your existing `ansible` project:
   - **Post-build Actions** > **Build Trigger** > Select “Build after other projects are built.”
     
  ![01 build triggers](https://github.com/user-attachments/assets/2e8b3f53-0dda-4a29-9716-8feb89e3e0ca)
     
3. Limit the number of builds retained to save server space by configuring **Build Discarder**:
   - Set the number of builds to keep (e.g., the last 2 or 5 builds).
     
![02 discard old builds](https://github.com/user-attachments/assets/6f15e4b0-26f0-43d4-998c-22133a1f118a)

### Configuring the Artifact Copy Step

1. In the `save_artifacts` project, add a **Build Step** and select **Copy artifacts from other project**.
2. Set the following configurations:
   - **Source Project**: Specify your existing `ansible` project as the source.
   - **Target Directory**: Specify `/home/ubuntu/ansible-config-artifact` as the location to store artifacts.
     
![03 build step](https://github.com/user-attachments/assets/25d898bf-3f82-4205-9383-e18595ec15b9)

### Testing the Setup

1. Make a small change in the `README.md` file within your `ansible-config-mgt` repository on the master branch.
   
![04 build successful](https://github.com/user-attachments/assets/8d89d29a-b4af-4c05-8aea-5b659e4b361f)

3. Commit the change to trigger the Jenkins pipeline.
   ![05 terminal build successful](https://github.com/user-attachments/assets/be1996f1-4f80-4a57-81c4-3044f7baf77d)


### Verifying Artifact Storage

Once the Jenkins pipeline completes, both jobs (`ansible` and `save_artifacts`) should run in sequence. Check `/home/ubuntu/ansible-config-artifact` to confirm that it contains the updated artifacts.

![06 git pull](https://github.com/user-attachments/assets/1c057260-bf77-4d6a-922a-bd58aa7d5b0b)


With this enhancement, your Jenkins pipeline is now more organized and resource-efficient, consolidating artifacts in a central directory for streamlined management.

---

### Step 2 - Refactor Ansible Code by Importing Other Playbooks into `site.yml`

Let’s see code re-use in action by importing other playbooks.

1. Within the `playbooks` folder, create a new file named `site.yml`. This file will now be considered an entry point into the entire infrastructure configuration. Other playbooks will be included here as references. In other words, `site.yml` will become a parent to all other playbooks, including `common.yml`, that you previously created. *(More on this structure will become clear soon)*.
   
2. Create a new folder in the root of the repository and name it `static-assignments`. This folder will hold all child playbooks, simply for organizational purposes. *(This is not an Ansible-specific concept, so you can adapt it as needed. The “static” prefix will become relevant shortly.)*
   
3. Move the `common.yml` file into the newly created `static-assignments` folder.

4. Inside `site.yml`, import the `common.yml` playbook:

   ```yaml
   ---
   - hosts: all
   - import_playbook: ../static-assignments/common.yml

   ```

   The code above uses the built-in `import_playbook` Ansible module.

5. Run the `ansible-playbook` command against the dev environment.

Since you need to apply some tasks to your dev servers and Wireshark is already installed, go ahead and create another playbook under `static-assignments`, named `common-del.yml`. In this playbook, configure the deletion of the Wireshark utility.

   ```yaml
   ---
   - name: update web, nfs, and db servers
     hosts: webservers, nfs, db
     remote_user: ec2-user
     become: yes
     become_user: root
     tasks:
       - name: delete wireshark
         yum:
           name: wireshark
           state: removed

   - name: update LB server
     hosts: lb
     remote_user: ubuntu
     become: yes
     become_user: root
     tasks:
       - name: delete wireshark
         apt:
           name: wireshark-qt
           state: absent
           autoremove: yes
           purge: yes
           autoclean: yes
   ```

6. Update `site.yml` with:

   ```yaml
   - import_playbook: ../static-assignments/common-del.yml
   ```

   Then run it against the dev servers:

   ```bash
   cd /home/ubuntu/ansible-config-mgt/
   ansible-playbook -i inventory/dev.yml playbooks/site.yaml
   ```

7. Confirm that Wireshark is deleted on all servers by running:

   ```bash
   wireshark --version
   ```

### Step 3 - Configure UAT Webservers with a Role 'Webserver'

We have our nice and clean dev environment, so let us put it aside and configure 2 new Web Servers as UAT. We could write tasks to configure Web Servers in the same playbook, but it would be too messy. Instead, we will use a dedicated role to make our configuration reusable.

1. Launch 2 fresh EC2 instances using the RHEL 8 image. We will use them as our UAT servers, so give them names accordingly - `Web1-UAT` and `Web2-UAT`.

   **Tip**: Remember to stop EC2 instances that you are not currently using to avoid extra charges. For now, only the 2 new RHEL 8 Web Servers and the existing Jenkins-Ansible server need to be up and running.

2. To create a role, make a directory called `roles/`, either relative to the playbook file or in `/etc/ansible/` directory.

   There are two ways to create this folder structure:

   - Use the Ansible utility `ansible-galaxy` inside the `ansible-config-mgt/roles` directory (create the `roles` directory upfront).

     ```bash
     mkdir roles
     cd roles
     ansible-galaxy init webserver
     ```

3. The complete folder structure should resemble the following [https://github.com/Rational-Being/ansible-config-mgt]. If you create it manually, you can skip creating `tests`, `files`, and `vars` directories or remove them if you used `ansible-galaxy`.

   ```
   └── webserver
       ├── README.md
       ├── defaults
       │   └── main.yml
       ├── handlers
       │   └── main.yml
       ├── meta
       │   └── main.yml
       ├── tasks
       │   └── main.yml
       └── templates
   ```

5. Update your inventory file `ansible-config-mgt/inventory/uat.yml` with the IP addresses of your 2 UAT Web servers.

   **NOTE**: Ensure you are using `ssh-agent` to SSH into the Jenkins-Ansible instance as in Project 11.

   ```yaml
   [uat-webservers]
   <Web1-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
   <Web2-UAT-Server-Private-IP-Address> ansible_ssh_user='ec2-user'
   ```

6. In the `/etc/ansible/ansible.cfg` file, uncomment the `roles_path` line and specify the full path to your roles directory:

   ```plaintext
   roles_path = /home/ubuntu/ansible-config-mgt/roles
   ```

   This allows Ansible to locate configured roles.

7. It’s time to start adding some logic to the `webserver` role. In the `tasks` directory, within `main.yml`, write the configuration tasks to perform the following:

   - Install and configure Apache (`httpd` service).
   - Clone the Tooling website from GitHub `https://github.com/<your-name>/tooling.git`.
   - Ensure the Tooling website code is deployed to `/var/www/html` on each UAT Web server.
   - Make sure the `httpd` service is started.

   Your `main.yml` may look like this:

   ```yaml
   ---
   - name: install apache
     become: true
     ansible.builtin.yum:
       name: "httpd"
       state: present

   - name: install git
     become: true
     ansible.builtin.yum:
       name: "git"
       state: present

   - name: clone a repo
     become: true
     ansible.builtin.git:
       repo: https://github.com/<your-name>/tooling.git
       dest: /var/www/html
       force: yes

   - name: copy html content to one level up
     become: true
     command: cp -r /var/www/html/html/ /var/www/

   - name: Start service httpd, if not started
     become: true
     ansible.builtin.service:
       name: httpd
       state: started

   - name: recursively remove /var/www/html/html/ directory
     become: true
     ansible.builtin.file:
       path: /var/www/html/html
       state: absent
   ```
```
