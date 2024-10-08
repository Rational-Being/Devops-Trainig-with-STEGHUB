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


### Save and Run the Build

1. After configuring your Jenkins job, **Save** the settings.
2. To trigger the first build, click on the **"Build Now"** button. If everything is configured correctly, the build will start successfully, and you’ll see it appear as **Build #1** at the bottom of the Jenkins dashboard.

3. To verify the build status, click on **Build #1** and navigate to the **"Console Output"**. If the output shows no errors and the build completed as expected, congratulations! You have successfully run your very first Jenkins build.

### Enhancing the Build: Automation and Artifacts

#### **Configure GitHub Webhook for Automatic Trigger:**

   - Navigate to your Jenkins job configuration by clicking **"Configure"**.
   - Under the **"Build Triggers"** section, enable **"GitHub hook trigger for GITScm polling"**. This ensures the job is triggered automatically whenever changes are pushed to the GitHub repository (via the webhook).

#### **Configure Post-Build Actions:**

   - Scroll down to the **"Post-build Actions"** section.
   - Add a **"Post-build Action"** and select **"Archive the artifacts"**.
   - Specify the files you want to archive. For example, if your build generates any files like logs or compiled code, you could use `**/*` to archive all files or specify certain file types like `*.php`.

#### **Test the Configuration:**

   - Save the updated configuration.
   - Now, make any small change to your GitHub repository (e.g., update the `README.md` file) and push the changes to the `master` branch.
   - Jenkins will detect the push through the webhook and automatically trigger a new build.
   - You can observe the build status and check the **"Artifacts"** generated under the build's details.

### Continuous Integration Flow

At this point, you've successfully configured an automated Jenkins job that listens for GitHub webhook triggers. Each time you push changes to the repository, a new build will be initiated. The process is seamless and reduces manual intervention.

- This "push" method (triggering builds via GitHub webhook) ensures changes are immediately built and tested.
- Other triggering methods include setting up **downstream** jobs (where one job triggers another) or **polling** GitHub periodically for changes.

#### Accessing Artifacts:
By default, any artifacts you archive will be stored on the Jenkins server. You can view them locally using:

```bash
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
```

This directory contains all archived files from your builds, allowing you to retrieve or inspect them as needed.


### Configure Jenkins to Copy Files to NFS Server via SSH

Now we have our artifacts saved locally on Jenkins server, the next step is to copy them to our NFS server in the `/mnt/apps` directory.

Jenkins is a highly extendable application and there are 1400+ plugins available. We will need a plugin called **"Publish Over SSH"**.

1. **Install "Publish Over SSH" Plugin**

   - On the main dashboard, select **"Manage Jenkins"** and choose **"Manage Plugins"**.
   - In the **"Available"** tab, search for **"Publish Over SSH"** plugin and install it.

2. **Configure the Job/Project to Copy Artifacts Over to NFS Server**

   - On the main dashboard, select **"Manage Jenkins"** and choose **"Configure System"**.
   - Scroll down to the **Publish over SSH** plugin configuration section and configure it to connect to your NFS server:
   
     - Provide a private key (content of the `.pem` file that you use to connect to the NFS server via SSH/Putty).
     - Arbitrary name.
     - Hostname - the private IP address of your NFS server.
     - Username - `ec2-user` (since the NFS server is based on EC2 with RHEL 8).
     - Remote directory - `/mnt/apps` (as our Web Servers use it as a mounting point to retrieve files from the NFS server).
     
   - Test the configuration and ensure the connection returns **Success**. Remember, that **TCP port 22** on the NFS server must be open to receive SSH connections.

3. **Configure Jenkins Job to Transfer Files**

   - Save the configuration, open your Jenkins job/project configuration page, and add another **"Post-build Action"**.
   - Configure it to send all files produced by the build into the previously defined remote directory. In our case, we want to copy all files and directories, so we use `**`. If you want to apply a particular pattern to define which files to send, use that syntax.

4. **Test the Setup**

   - Save this configuration and go ahead, change something in the `README.MD` file in your GitHub Tooling repository.
     
         ![10 edit readme](https://github.com/user-attachments/assets/5e31c203-2d2b-45da-9d4b-8d5995064427)

   - The webhook will trigger a new job, and in the **"Console Output"** of the job, you will see something like this:
   
     ```
     SSH: Transferred 25 file(s)
     Finished: SUCCESS
     ```

5. **Verify File Transfer**

   - To make sure the files in `/mnt/apps` have been updated, connect via SSH/Putty to your NFS server and check the `README.MD` file:
   
     ```bash
     cat /mnt/apps/README.md
     ```


     
