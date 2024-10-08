# Tooling Website deployment automation with Continuous Integration
## Introduction to Jenkins

In this project, the primary goal is to automate the deployment of a tooling website by implementing Continuous Integration (CI) using Jenkins on an Ubuntu server. This process will streamline the workflow by ensuring that code changes are automatically built, tested, and deployed with minimal manual intervention. By leveraging Jenkins, we aim to create an efficient pipeline that handles code versioning, testing, and deployment to the production environment in a reliable, repeatable, and consistent manner. This provides us with the follwoing benefit:

1. **Automation of Repetitive Tasks**: Jenkins allows us to automate the entire build and deployment process, saving time and reducing the potential for human errors.
2. **Continuous Integration**: Every time code is pushed to the repository, Jenkins automatically triggers a build, ensuring that any issues or bugs are caught early in the development process.
3. **Improved Collaboration**: As Jenkins integrates with GitHub, all team members can collaborate efficiently, with the assurance that their changes will be tested and deployed automatically.
4. **Efficient Deployment**: Jenkins ensures that deployment is consistent across environments, whether to development, staging, or production servers.
5. **Extensibility with Plugins**: Jenkins has a large repository of plugins that can be integrated into the pipeline, offering functionalities such as notifications, testing frameworks, and deployment strategies.


## Jenkins Installation and Configuration

#### **Install Jenkins:**
   
   1. **Add Jenkins Repository to the Package Manager:**
      First, add the Jenkins repository and the required keys so your system can locate the Jenkins packages:
      ```bash
    sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
      https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
    echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
      https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
      /etc/apt/sources.list.d/jenkins.list > /dev/null
    sudo apt-get update
    sudo apt-get install jenkins
      ```

![01  wget](https://github.com/user-attachments/assets/737df1de-ea1f-4faa-b1fd-c417e7972ca2)


   2. **Install Jenkins:**
      Now install Jenkins using the package manager:
      ```bash
      sudo apt-get install jenkins -y
      ```

![02 install jekins](https://github.com/user-attachments/assets/4f0a7970-c0ea-4deb-8834-1fa9e6913adb)


   3. **Start and Enable Jenkins:**
      After installation, start the Jenkins service and ensure it starts automatically on system boot:
      ```bash
      sudo systemctl start jenkins
      sudo systemctl enable jenkins
      ```
      
![03 jekins status](https://github.com/user-attachments/assets/3e9024fd-b6f0-49b0-8485-796c2e4d12a9)


   4. **Open Jenkins Web Interface:**
      Jenkins by default runs on port `8080`. To access Jenkins, open a browser and navigate to:
      ```
      http://<your-server-ip>:8080
      ```

![05 open 8080](https://github.com/user-attachments/assets/19ceef4b-7a23-4e60-ad52-ee333eb52f8a)

![04 jekins first page](https://github.com/user-attachments/assets/48e39297-3222-4e6f-94da-ab0bf4151a7c)


   You will be prompted to unlock Jenkins. Retrieve the initial admin password by running the following command:
      ```bash
      sudo cat /var/lib/jenkins/secrets/initialAdminPassword
      ```
      Enter the password on the web interface to proceed.
      
![06 jekins password](https://github.com/user-attachments/assets/b0f632fa-0275-465e-abfa-8434e4fadc7e)


 5. **Install Necessary Plugins:**
      After the initial setup, you can install additional plugins to enhance your CI/CD pipeline.

![07 install plugins](https://github.com/user-attachments/assets/025f4c45-7906-4453-a7f0-0bc48cd158ff)


![08 installing](https://github.com/user-attachments/assets/a1e3cb3e-9496-4fd4-a194-2ab7ed96f467)


#### **Configure Jenkins:**

   1. **Set up an Admin User and Initial Configuration:**
      - Follow the prompts on the web interface to create an admin user. Choose a username, password, and email for the admin account.
      - Jenkins will ask if you want to install suggested plugins or select specific ones. Choose "Install suggested plugins" to install commonly used plugins.

   2. **Install Necessary Plugins:**
      After the initial setup, you can install additional plugins to enhance your CI/CD pipeline.
      
      - **Git Plugin:** This allows Jenkins to integrate with GitHub and other Git repositories.
      - **PHP Plugin:** Useful for PHP-specific build tasks and linting.
      - **Build Pipeline Plugin:** Helps you visualize the pipeline stages of your project.
      - **Other Plugins:** Consider plugins for automated testing, code analysis (e.g., Checkstyle), and notifications (e.g., Slack).

      To install plugins:
      - Navigate to `Manage Jenkins` > `Manage Plugins` > `Available`.
      - Search for the desired plugin and click `Install without restart`.

After completing these steps, Jenkins is ready to automate your CI/CD pipeline for the tooling website project.
