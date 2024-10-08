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


6. **Set up an Admin User and Initial Configuration:**
     Follow the prompts on the web interface to create an admin user. Choose a username, password, and email for the admin account.


### Configuring GitHub Repository for the Tooling Website

#### **Set Up Webhook in GitHub:**

   1. **Configure Webhook in GitHub Repository Settings:**
      - Go to the **GitHub repository** you created for the tooling website.
      - In the repository, click on **Settings** > **Webhooks** > **Add webhook**.
      - Paste the Jenkins webhook URL in the **Payload URL** field (e.g., `http://<your-jenkins-server>:8080/github-webhook/`).
      - Set **Content type** to `application/json`.
      - In the **Which events would you like to trigger this webhook?** section, select "Just the push event".
      - Click **Add Webhook**.
        
   ![10 github webhook](https://github.com/user-attachments/assets/e11fd035-1b2b-4dab-902c-e30b88eda752)

            
   2. **Generate a Webhook URL in Jenkins:**
      - In Jenkins, create a new **Freestyle Project** or **Pipeline** job that will automate the deployment or any other task for the tooling website.
      - In the job configuration, under **Source Code Management**, select "Git" and add the GitHub repository URL (https://github.com/<username>/tooling-website.git).
      - Scroll down to **Build Triggers** and check **GitHub hook trigger for GITScm polling**.
      - Jenkins will now generate a webhook URL for the job, which can be used in GitHub to trigger the build on any code changes.
      
  ![09 add git repo](https://github.com/user-attachments/assets/557832f8-56c0-4e3b-8623-1fd67c888d88)


   4. **Test the Webhook:**
      - Make a change to the repository (e.g., update a file and push it to GitHub).

      ![10 edit readme](https://github.com/user-attachments/assets/5e31c203-2d2b-45da-9d4b-8d5995064427)

      - This should trigger a build job in Jenkins, and you should see the automation process being carried out in Jenkins.

By setting up the GitHub webhook with Jenkins, any changes made to the tooling website's code will automatically trigger Jenkins jobs for deployment or any other predefined actions, streamlining the Continuous Integration (CI) process.




     
