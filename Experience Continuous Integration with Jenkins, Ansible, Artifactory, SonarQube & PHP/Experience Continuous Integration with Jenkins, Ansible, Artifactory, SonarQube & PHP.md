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


# Configuring Ansible For Jenkins Deployment

In previous projects, I have been launching Ansible commands manually from a CLI. Now, with Jenkins, I will start running Ansible from Jenkins UI.

To do this,

- Navigate to Jenkins URL  
- Install & Open Blue Ocean Jenkins Plugin
  
![02 blue ocean description](https://github.com/user-attachments/assets/02a33de9-59f7-466c-8986-1dcfc4a44644)
  
- Create a new pipeline
  
  ![03 connect pipelin](https://github.com/user-attachments/assets/59c7b313-f64b-434e-9568-04e3ef0e1f25)


### Connect Jenkins with GitHub  

- Login to GitHub & Generate an Access Token  
- Copy Access Token  
- Paste the token and connect  
- Create a new Pipeline
  
![04 newly created pipeline](https://github.com/user-attachments/assets/255c8934-1fe5-4658-8846-80d1b38d65dd)

At this point, you may not have a `Jenkinsfile` in the Ansible repository, so Blue Ocean will attempt to give you some guidance to create one. But we do not need that. We will rather create one ourselves. So, click on **Administration** to exit the Blue Ocean console.

Here is our newly created pipeline. It takes the name of your GitHub repository.  
Let us create our `Jenkinsfile`.

Inside the Ansible project, create a new directory `deploy` and start a new file `Jenkinsfile` inside the directory.

Add the code snippet below to start building the `Jenkinsfile` gradually. This pipeline currently has just one stage called **Build**, and the only thing we are doing is using the shell script module to echo **Building Stage**:

```groovy
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    sh 'echo "Building Stage"'
                }
            }
        }
    }
}
```

Now go back into the Ansible pipeline in Jenkins, and select **Configure**.

Scroll down to **Build Configuration** section and specify the location of the `Jenkinsfile` at `deploy/Jenkinsfile`.

Back to the pipeline again, this time click **Build now**.

![05 success build](https://github.com/user-attachments/assets/a195b39e-b7a7-486c-a9c4-37455d954fa2)

This will trigger a build, and you will be able to see the effect of our basic `Jenkinsfile` configuration by going through the console output of the build.

![06 sucees build](https://github.com/user-attachments/assets/e4418c68-f67c-4e60-be40-42fc9ba59dcf)

To really appreciate and feel the difference of Cloud Blue UI, it is recommended to try triggering the build again from the Blue Ocean interface.

- Click on **Blue Ocean**  
- Select your project  
- Click on the play button against the branch  

Notice that this pipeline is a **multibranch** one. This means, if there were more than one branch in GitHub, Jenkins would have scanned the repository to discover them all, and we would have been able to trigger a build for each branch.

Let us see this in action.

- Create a new git branch and name it `feature/jenkinspipeline-stages`.  
  Currently, we only have the **Build** stage. Let us add another stage called **Test**. Paste the code snippet below and push the new changes to GitHub.

```
pipeline {
    agent any

  stages {
    stage('Build') {
      steps {
        script {
          sh 'echo "Building Stage"'
        }
      }
    }

    stage('Test') {
      steps {
        script {
          sh 'echo "Testing Stage"'
        }
      }
    }
    }
}
```
 
 
 # To make your new branch show up in Jenkins, we need to tell Jenkins to scan the repository.

- Click on the **Administration** button  
- Navigate to the Ansible project and click on **Scan repository now**  
- Refresh the page, and both branches will start building automatically. You can go into Blue Ocean and see both branches there too.  
- In Blue Ocean, you can now see how the `Jenkinsfile` has caused a new step in the pipeline launch build for the new branch.  

![07 buid feauture branh success](https://github.com/user-attachments/assets/7ecbe3dd-80ec-40a5-b41b-22308ac49322)

---

## LET'S MAKE THIS MORE INTERESTING!

1. **Create a pull request** to merge the latest code into the `main` branch.
   ![08 merge pull request](https://github.com/user-attachments/assets/61a17176-f703-4c6e-b61a-c998a2841721)
2. After merging the **PR**, go back into your terminal and switch into the `main` branch.  
3. **Pull the latest change.**
    ![09 task checkout and pull](https://github.com/user-attachments/assets/118c9f57-9e9f-447f-be99-afe9170f633f)
4. **Create a new branch**, add more stages into the `Jenkinsfile` to simulate the below phases. *(Just add an `echo` command like we have in `build` and `test` stages)*:
   1. **Package**  
   2. **Deploy**  
   3. **Clean up**
      
![10 task add more stages](https://github.com/user-attachments/assets/9a49779a-689d-43c4-a700-3316e3c1ba40)

5. **Verify in Blue Ocean** that all the stages are working, then merge your feature branch to the `main` branch.
   
   ![11 taskstage](https://github.com/user-attachments/assets/a1266ab5-4050-41ab-a598-3af72690e8d9)

6. Eventually, your `main` branch should have a successful pipeline like this in Blue Ocean.  

  ![12 task pull](https://github.com/user-attachments/assets/36c3072a-ecac-4306-8af7-343f7cb4a09e)

 















