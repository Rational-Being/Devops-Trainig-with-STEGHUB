# Experience Continuous Integration with Jenkins | Ansible | Artifactory | SonarQube | PHP

## Simulating a typical CI/CD Pipeline for a PHP Based application

**In this Project, I will create a pipeline that  simulates continuous integration and delivery. Target end to end CI/CD pipeline is represented by the diagram below.**

![format](https://github.com/user-attachments/assets/f050b19e-8adb-4983-b161-d99f85367526)

It is important to know that both `Tooling` and `TODO` Web Applications (the applications that will be used in this project) are based on an interpreted (scripting) language (PHP). It means, it can be deployed directly onto a server and will work without compiling the code to a machine language.

The problem with that approach is, it would be difficult to package and version the software for different releases. And so, in this project, we will be using a different approach for releases, rather than downloading directly from git, we will be using Ansible `uri module`.


To get started, we will focus on these environments initially.

    Ci
    Dev
    Pentest

Both SIT - For System Integration Testing and UAT - User Acceptance Testing do not require a lot of extra installation or configuration. They are basically the webservers holding our applications. But Pentest - For Penetration testing is where we will conduct security related tests, so some other tools and specific configurations will be needed. In some cases, it will also be used for Performance and Load testing. Otherwise, that can also be a separate environment on its own. It all depends on decisions made by the company and the team running the show.

What we want to achieve, is having Nginx to serve as a reverse proxy for our sites and tools.

---

### **Define the Roles of Each Environment**

- **CI (Continuous Integration):** Automate builds, tests, and deployments.
  - Tools: Jenkins, SonarQube, Artifactory, Ansible.

- **Dev (Development):** Active development and testing environment.
  - Tools: Web server (Nginx + PHP-FPM).

- **Pentest (Penetration Testing):** Security and performance testing.
  - Tools: Nginx reverse proxy, security tools (e.g., OWASP ZAP, Burp Suite), load testing tools (e.g., JMeter).


### **Directory Structure**

Create a directory structure that separates inventory files for each environment.

```bash
touch ansible_CI_CD/inventory/{ci,dev,pentest,pre-prod,prod,sit,uat}
```

---

### **2. Create Inventory Files**

Navigate to each folder and create an inventory file:

#### **CI Inventory File**
Path: `ansible_CI_CD/inventory/ci/inventory`

```ini
[jenkins]
<Jenkins-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[sonarqube]
<SonarQube-Private-IP-Address>

[artifact_repository]
<Artifact_Repository-Private-IP-Address>
```

---

#### **Dev Inventory File**
Path: `ansible_CI_CD/inventory/dev/inventory`

```ini
[tooling]
<Tooling-Web-Server-Private-IP-Address>

[todo]
<Todo-Web-Server-Private-IP-Address>

[nginx]
<Nginx-Private-IP-Address>

[db:vars]
ansible_user=ec2-user
ansible_python_interpreter=/usr/bin/python

[db]
<DB-Server-Private-IP-Address>
```

---

#### **Pentest Inventory File**
Path: `ansible_CI_CD/inventory/pentest/inventory`

```ini
[pentest:children]
pentest-todo
pentest-tooling

[pentest-todo]
<Pentest-for-Todo-Private-IP-Address>

[pentest-tooling]
<Pentest-for-Tooling-Private-IP-Address>
```

We have used `pentest:children` above because, we want to have a group called pentest which covers Ansible execution against both pentest-todo and pentest-tooling simultaneously.
 

# Ansible Roles for CI Environment

### Add two more roles to Ansible:
- **SonarQube**  
  
- **Artifactory**

![01 create role](https://github.com/user-attachments/assets/bd513551-14b6-45d7-8737-681ebd32c667)


## Why do we need SonarQube?

SonarQube is an open-source platform developed by SonarSource for continuous inspection of code quality. It is used to perform automatic reviews with static analysis of code to detect bugs, code smells, and security vulnerabilities. 

## Why do we need Artifactory?

Artifactory is a product by JFrog that serves as a binary repository manager. The binary repository is a natural extension to the source code repository, in that the outcome of your build process is stored. It can be used for certain other automation, but we will use it strictly to manage our build artifacts.

 
 
 
 
 
 
 















