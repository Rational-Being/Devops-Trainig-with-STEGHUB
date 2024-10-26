### Project Overview
- **Objective:** Learn Ansible Configuration Management and declarative programming with YAML.

### Introduction: Ansible Client as a Jump Server (Bastion Host)
- **Jump Server/Bastion Host:** Acts as an intermediary to secure access to internal networks, protecting servers from direct exposure to the Internet. It enhances security by limiting SSH access to the servers through this intermediary.

---

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

---

### Step 2 - Set Up Development Environment with Visual Studio Code
1. **Install an IDE:**
   - Install Visual Studio Code (recommended).
   
2. **Configure VSC:**
   - Connect it to the GitHub repository (`ansible-config-mgt`).

3. **Clone the Repository:**
   ```bash
   git clone <ansible-config-mgt repo link>
   ```


