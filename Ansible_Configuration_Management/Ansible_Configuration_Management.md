# Learn Ansible Configuration Management and declarative programming with YAML.

### Introduction: Ansible Client as a Jump Server (Bastion Host)
- **Jump Server/Bastion Host:** Acts as an intermediary to secure access to internal networks, protecting servers from direct exposure to the Internet. It enhances security by limiting SSH access to the servers through this intermediary.

### Step 1 - Install and Configure Ansible on an EC2 Instance
1. **Prepare the EC2 Instance:**
   - Update the name tag of the Jenkins EC2 instance to `Jenkins-Ansible`.
   - Use this instance for running Ansible playbooks.

2. **Create a New Repository:**
   - Name it `ansible-config-mgt` in your GitHub account.

3. **Install Ansible:**
   ```bash
   sudo apt update
   sudo apt install ansible
   ```
![01 apt install](https://github.com/user-attachments/assets/f25428e6-c30b-46bd-b64b-b73e68d2312c)

![001 ansible version](https://github.com/user-attachments/assets/d2b78487-6167-47f1-9f8f-5a225a8816ae)

4. **Configure Jenkins to Archive Artifacts:**
   - Create a new Freestyle project named `ansible` in Jenkins.
  ![02 frestyle project](https://github.com/user-attachments/assets/d79c0521-4c33-4e74-a3ee-bc770a357c58)
     
   - Link it to the `ansible-config-mgt` repository.
  ![03 repo](https://github.com/user-attachments/assets/f4f3eec1-f491-40ef-8cce-6c0335e02727)

   - Set up a GitHub webhook to trigger the `ansible` build.
   - Add a post-build action to archive all files (`**` pattern).
     ![04 archive](https://github.com/user-attachments/assets/c935e438-410c-4ba8-a33c-e2677d23309b)


5. **Testing the Setup:**
   - Make a change to the `README.md` in the master branch.
   - Verify that the build is triggered automatically and artifacts are saved in:
     ```
     /var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/
     ```
   - Only trigger the build for changes in the main/master branch.

### Step 2 - Set Up Development Environment with Visual Studio Code
1. **Install an IDE:**
   - Install Visual Studio Code (recommended).
   
2. **Configure VSC:**
   - Connect it to the GitHub repository (`ansible-config-mgt`).

3. **Clone the Repository:**
   ```bash
   git clone <ansible-config-mgt repo link>
   ```

### Step 3 - Begin Ansible Development

1. In your `ansible-config-mgt` GitHub repository, create a new branch that will be used for the development of a new feature.

   **Tip:** Give your branches descriptive and comprehensive names. For example, if you use Jira or Trello as a project management tool, include the ticket number (e.g., `PRJ-145`) in the name of your branch, and add a topic and a brief description of what this branch is about—such as a bugfix, hotfix, feature, or release (e.g., `feature/prj-145-lvm`).

2. Checkout the newly created feature branch to your local machine and start building your code and directory structure.

![05 checkout](https://github.com/user-attachments/assets/f22014cb-c992-404f-b36a-100c0a7fe950)

3. Create a directory and name it `playbooks`—it will be used to store all your playbook files.

4. Create a directory and name it `inventory`—it will be used to keep your hosts organized.

5. Within the `playbooks` folder, create your first playbook, and name it `common.yml`.

6. Within the `inventory` folder, create an inventory file for each environment (Development, Staging, Testing, and Production): `dev`, `staging`, `uat`, and `prod` respectively. These inventory files use `.ini` style syntax to configure Ansible hosts.

![06 vs code](https://github.com/user-attachments/assets/7b1cd49b-f36c-4193-afe5-4751342a3047)

### Understanding Ansible Inventory Files

1. **Basic Structure**
   - Inventory files in Ansible are divided into groups of servers (hosts). Each group is defined by a name enclosed in square brackets, followed by a list of servers belonging to that group.

2. **Group Definition**
   - A group is a collection of servers that you want to manage together. The group name is written inside square brackets `[ ]`.
   - For example:
     ```ini
     [webservers]
     ```
     This defines a group named `webservers`.

3. **Adding Hosts to a Group**
   - After defining a group, you list the servers (hosts) that belong to that group. Each server can be defined by its hostname or IP address.
   - Optionally, you can use variables like `ansible_host` to specify the IP address of the server.
   - For example:
     ```ini
     [webservers]
     webserver1 ansible_host=192.168.1.10
     webserver2 ansible_host=192.168.1.11
     ```
   - In this case, `webserver1` and `webserver2` are hostnames, and their corresponding IP addresses are specified using `ansible_host`.

4. **Grouping Multiple Servers**
   - You can create multiple groups within the same inventory file to organize different types of servers.
   - For example:
     ```ini
     [webservers]
     webserver1 ansible_host=192.168.1.10
     webserver2 ansible_host=192.168.1.11

     [dbservers]
     dbserver1 ansible_host=192.168.1.12
     dbserver2 ansible_host=192.168.1.13
     ```
   - Here, two groups (`webservers` and `dbservers`) are defined, each containing different servers.


### **Step 4 - Set up an Ansible Inventory**

An Ansible inventory file defines the hosts and groups of hosts upon which commands, modules, and tasks in a playbook operate. Since our intention is to execute Linux commands on remote hosts and ensure that it is the intended configuration on a particular server that occurs, it is important to have a way to organize our hosts in such an inventory.

Save the below inventory structure in the `inventory/dev` file to start configuring your development servers. Ensure to replace the IP addresses according to your own setup.

**Note:** Ansible uses TCP port 22 by default, which means it needs to SSH into target servers from the Jenkins-Ansible host. For this, you can implement the concept of `ssh-agent`. Now, you need to import your key into `ssh-agent`:

```bash
eval `ssh-agent -s`
ssh-add <path-to-private-key>
```

Confirm the key has been added with the command below; you should see the name of your key:

```bash
ssh-add -l
```

Now, SSH into your Jenkins-Ansible server using `ssh-agent`:

```bash
ssh -A ubuntu@public-ip
```

### **Step 5 - Create a Common Playbook**

It is time to start giving Ansible the instructions on what you need to be performed on all servers listed in `inventory/dev`.

In `common.yml` playbook, you will write configuration for repeatable, reusable, and multi-machine tasks that are common to systems within the infrastructure.

Update your `playbooks/common.yml` file with the following code:

```yaml
---
- name: update web, nfs and db servers
  hosts: webservers, nfs, db
  become: yes
  tasks:
    - name: ensure wireshark is at the latest version
      yum:
        name: wireshark
        state: latest

    - name: create a directory
      file:
        path: /home/ansible
        state: directory
        mode: '0755'

    - name: create a file inside the directory
      copy:
        content: "This is a test file for Ansible playbook tasks.\n"
        dest: /home/ansible/test.txt

    - name: change timezone to UTC
      command: timedatectl set-timezone UTC

    - name: run a shell script
      shell: |
        echo "Hurray!!! I am writing an Ansible Play book" > /home/ansible/output.txt
        date >> /home/ansible/output.txt

- name: update LB server
  hosts: lb
  become: yes
  tasks:
    - name: Update apt repo
      apt: 
        update_cache: yes

    - name: ensure wireshark is at the latest version
      apt:
        name: wireshark
        state: latest

    - name: create a directory
      file:
        path: /home/ansible
        state: directory
        mode: '0755'

    - name: create a file inside the directory
      copy:
        content: "This is a test file for Ansible playbook tasks.\n"
        dest: /home/ansible/test.txt

    - name: change timezone to UTC
      command: timedatectl set-timezone UTC

    - name: run a shell script
      shell: |
        echo "Hurray!!! I am writing an Ansible Play book" > /home/ansible/output.txt
        date >> /home/ansible/output.txt

```

![07 update common](https://github.com/user-attachments/assets/b54470fb-494a-4c65-aa08-278f5885e6fc)


### **Step 6 - Update GIT with the Latest Code**

Now all of your directories and files live on your machine, and you need to push changes made locally to GitHub.

Now that you have a separate branch, you need to raise a Pull Request (PR), get your branch peer-reviewed, and merged into the master branch.

Commit your code to GitHub:

1. Use Git commands to add, commit, and push your branch to GitHub.
   ```bash
   git status
   git add <selected files>
   git commit -m "commit message"
   ```

2. Create a Pull Request (PR).
   ![08 compare pull request](https://github.com/user-attachments/assets/e1628d18-ce9f-407e-9e11-fe580fe720ef)

4. Wear the hat of another developer for a second and act as a reviewer.
5. If the reviewer is happy with your new feature development, merge the code into the master branch.
   ![09 merge](https://github.com/user-attachments/assets/ff408942-d1c0-408e-9af8-220be59d632e)

7. Head back to your terminal, checkout from the feature branch into the master, and pull down the latest changes.

Once your code changes appear in the master branch, Jenkins will do its job and save all the files (build artifacts) to `/var/lib/jenkins/jobs/ansible/builds/<build_number>/archive/` directory on the Jenkins-Ansible server.


### **Step 7 - Run the first Ansible test**

Now, it is time to execute the `ansible-playbook` command and verify if your playbook actually works:

1. **Set up your VSCode to connect to your instance**. Now run your playbook using the command:

   ```bash
   cd ansible-config-mgt
   ansible-playbook -i inventory/dev.yml playbooks/common.yml
   ```

2. **Run the playbook again** to ensure consistency:

   ```bash
   ansible-playbook -i inventory/dev.yml playbooks/common.yml
   ```

**Note:** Make sure you're in your `ansible-config-mgt` directory before you run the above command.

You can go to each of the servers and check if Wireshark has been installed by running `which wireshark` or `wireshark --version`.

![10 wireshark](https://github.com/user-attachments/assets/5bcc1c15-2ff4-4697-ad12-4f22acedef8e)

