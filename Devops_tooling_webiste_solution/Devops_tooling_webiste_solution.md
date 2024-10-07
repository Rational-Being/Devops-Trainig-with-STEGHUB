# DevOps Tooling Website Solution

In this project, I will introduce a set of DevOps tools that will help my team in day to day activities in managing, developing, testing, deploying and monitoring different projects. I will introduce a single DevOps Tooling Solution that will consist of:

### 1. **Jenkins**
Jenkins is an **automation server** that helps you build and manage **CI/CD pipelines** (Continuous Integration/Continuous Deployment). Think of it as a tool that automatically pulls in code changes, runs tests, builds the code into an application, and even deploys it to production, all without manual intervention. It saves time by automating these repetitive tasks and helps to ensure that the code is always in a deployable state.

### 2. **Kubernetes**
Kubernetes is a **container orchestration system**. Containers are a way to package applications so they can run anywhere (e.g., on different computers or in the cloud). Kubernetes helps automate the deployment, scaling, and management of these containers. If you imagine containers as "boxes" that hold your application, Kubernetes is like the "warehouse manager" that decides how many boxes are needed, where to place them, and makes sure they run smoothly.

### 3. **JFrog Artifactory**
JFrog Artifactory is a **repository manager**. When you're building software, you often need to store and manage various packages, libraries, and binaries. Artifactory acts as a central storage hub where all these components are stored securely. It supports all major programming languages and tools, and it integrates well with Jenkins and other CI/CD systems to manage versioned builds.

### 4. **Rancher**
Rancher is a **management platform for Kubernetes and Docker**. Docker is a tool for creating containers, and Kubernetes is for managing them. Rancher simplifies how you work with these technologies by providing a user-friendly interface to deploy and manage your containers and Kubernetes clusters. It's like a control center that helps you keep track of and manage everything happening with your containers in production.

### 5. **Grafana**
Grafana is a tool for **visualizing data** and **monitoring systems**. It collects and displays real-time data in beautiful, customizable dashboards. For example, you can monitor server performance, application health, or even track business metrics. Grafana helps you stay informed about what's happening within your infrastructure, and you can set it up to alert you when something is wrong.

### 6. **Prometheus**
Prometheus is an **open-source monitoring system** that works closely with Grafana. It collects metrics (data about how your systems are running) and stores them in a time-series database. It also allows you to set up alerts based on those metrics. For instance, you can monitor CPU usage or response times and get alerted if something goes beyond a certain threshold.

### 7. **Kibana**
Kibana is a **visualization tool for Elasticsearch**, which is part of the Elastic Stack. Elasticsearch stores and searches through large volumes of log data. Kibana takes that data and presents it in visual dashboards. You use Kibana to analyze logs, troubleshoot issues, and gain insights into what’s happening across your systems by exploring the collected data.

**All these tools are part of the DevOps toolkit to make software development, deployment, and monitoring more efficient and automated.**

## Step 1 - Prepare NFS Server

NFS (Network File System) allows you to share directories across multiple servers.

1. **Install NFS Kernel Server**:
   On the storage server, install the NFS server package:
   ```bash
   sudo apt install nfs-kernel-server -y
   ```
   
![01 Nfs kernel install](https://github.com/user-attachments/assets/ba2b1e96-5d64-4e1a-ac63-37b1733478b6)


2. **Create Shared Directories**:
   Decide which directories you want to share across the web and database servers. For example, create a directory for shared files:
   ```bash
   sudo mkdir -p /mnt/nfs_share
   ```

3. **Set Permissions on Shared Directory**:
   - Set the permissions to allow proper access:
   ```bash
   sudo chown nobody:nogroup /mnt/nfs_share
   sudo chmod 777 /mnt/nfs_share
   ```

##### **Configure NFS Exports**

1. **Edit the Exports File**:
   Add the directories you want to share to `/etc/exports`. For example:
   ```bash
   sudo vim /etc/exports
   ```

2. **Add Export Rules**:
   Export the shared directory to specific clients (web and database servers). Example configuration:
   ```
   /mnt/nfs_share    <web_server_ip>(rw,sync,no_subtree_check)
   ```
   Explanation:
   - `<web_server_ip>` and `<db_server_ip>`: IP addresses of the web and database servers.
   - `rw`: Allows read-write access.
   - `sync`: Ensures changes are written to disk before replies are sent.
   - `no_subtree_check`: Disables subtree checking for better performance.

3. **Apply the Export Configuration**:
   Reload the NFS server to apply the changes:
   ```bash
   sudo exportfs -ra
   ```
   ![05 export](https://github.com/user-attachments/assets/78fe0d81-65ec-4c98-b9a5-2ced436e3f0e)


4. **Start and Enable NFS Service**:
   Ensure the NFS server is running and set to start on boot:
   ```bash
   sudo systemctl start nfs-kernel-server
   sudo systemctl enable nfs-kernel-server
   ```
**To allow access to the NFS server, configure `ufw` (Uncomplicated Firewall) to open the necessary ports, follow these steps:**

Check which port is used by NFS and open it using Security Groups (add new Inbound Rule)

```
rpcinfo -p | grep nfs
```

![)9 nfs status](https://github.com/user-attachments/assets/085b14f3-19a3-45c2-b9e7-cb3967e6c5ea)


1. **First, verify if UFW is installed and enabled on your storage server**:
   ```bash
   sudo ufw status
   ```

2. **Allow Specific Ports for NFS**:

   NFS uses a combination of TCP and UDP ports. Specifically, you'll need to open the following ports:
   - TCP 111: Used by portmapper (RPC service).
   - UDP 111: Also used by portmapper.
   - UDP 2049: Default NFS port for data transfer.

   Run the following commands to allow access through these ports:

   ```bash
   sudo ufw allow 111/tcp
   sudo ufw allow 111/udp
   sudo ufw allow 2049/udp
   ```
![03 allow nfs port](https://github.com/user-attachments/assets/0147508a-61b4-4a20-82a9-6564e5a0d2e6)

3. **Check UFW Rules**:

   After adding the rules, you can verify that the ports are open with the following command:
   ```bash
   sudo ufw status
   ```

   You should see lines that indicate TCP 111, UDP 111, and UDP 2049 are allowed.

![05 ufw status](https://github.com/user-attachments/assets/067d2c7c-f922-4c92-af19-b3350b30bec8)


#### **Mounting NFS on Web and Database Servers**

Once the NFS server is configured, you'll need to mount the NFS share on both the web server and the database server.

##### **Install NFS Client**

1. **Install the NFS Client**:
   On both the web and database servers, install the NFS client package:
   ```bash
   sudo apt install nfs-common -y
   ```

##### **Mount the NFS Share**

1. **Create a Mount Directory**:
   On both the web and database servers, create a directory where the NFS share will be mounted. Example:
   ```bash
   sudo mkdir -p /mnt/nfs_share
   ```

2. **Mount the NFS Share**:
   Use the `mount` command to mount the NFS share from the storage server:
   ```bash
   sudo mount <storage_server_ip>:/mnt/nfs_share /mnt/nfs_share
   ```
   - Replace `<storage_server_ip>` with the IP address of your storage server.

   
![nfs mount](https://github.com/user-attachments/assets/db66c968-aa5b-4ffe-9897-75cbca37a932)


3. **Verify the Mount**:
   Confirm that the NFS share is mounted correctly:
   ```bash
   df -h
   ```
   You should see the NFS


## Step 2 — Configure the database server

Install MySQL server
Create a database and name it tooling
Create a database user and name it webaccess
Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidrmount point listed.

![databse setup](https://github.com/user-attachments/assets/deb91b90-9e6e-4c4d-b8f7-f9860deef7ca)

![grant priveiledges](https://github.com/user-attachments/assets/1b361db9-4432-4f9b-b94f-c47b02ab22f6)


