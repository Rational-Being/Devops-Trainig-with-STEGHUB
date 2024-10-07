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

Install NFS server, configure it to start on reboot and make sure it is up and running

```
sudo apt update
```

```
sudo apt install nfs-kernel-server -y
```


