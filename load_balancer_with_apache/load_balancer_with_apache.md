# **Documentation: Configuring Apache as a Load Balancer**

In this project, we will set up **Apache Load Balancer** on an **Ubuntu 20.04 EC2 instance** (named `Project-8-apache-lb`) and configure it to distribute traffic between two web servers. This documentation will walk you through creating and configuring the load balancer and testing it to ensure the traffic is distributed evenly across the web servers.

---

## **Prerequisites**
- You have two web servers already running on EC2 (or any infrastructure) with a basic web application.
- Ensure that the web servers are accessible over HTTP (port 80).
- The private IP addresses of the web servers are known.

---

## **Steps to Configure Apache as a Load Balancer**

### **1. Create an Ubuntu Server EC2 Instance (Project-8-apache-lb)**

- Launch a new EC2 instance using **Ubuntu 20.04** as the AMI and name it **Project-8-apache-lb**.
- Ensure you configure the security group to allow traffic on **TCP port 80**.
- SSH into the instance once it's running.

### **2. Install and Configure Apache Load Balancer**

#### **Update the System and Install Apache**
Run the following commands to update the package lists and install the required packages:
```bash
sudo apt update
sudo apt install apache2 -y
sudo apt-get install libxml2-dev -y
```

#### **Enable Apache Load Balancer Modules**

Enable the necessary Apache modules for load balancing and proxying:
```bash
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic
```

Restart the Apache service to apply changes:
```bash
sudo systemctl restart apache2
```

#### **Verify Apache Installation**
Check if the Apache service is running:
```bash
sudo systemctl status apache2
```
You should see a message indicating that **apache2** is active and running.

---

### **3. Configure Load Balancing**

#### **Modify Apache Virtual Host Configuration**

Edit the default Apache virtual host configuration file:
```bash
sudo vi /etc/apache2/sites-available/000-default.conf
```

Inside the `<VirtualHost *:80>` block, add the following configuration to define your load balancer:

