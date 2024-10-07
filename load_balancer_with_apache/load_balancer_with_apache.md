# Configuring Apache as a Load Balancer

In this project, we will set up **Apache Load Balancer** on an Ubuntu and configure it to distribute traffic between two web servers. This documentation will walk you through creating and configuring the load balancer and testing it to ensure the traffic is distributed evenly across the web servers.


## **Prerequisites**
- You have two web servers already running (or any infrastructure) with a basic web application.
- Ensure that the web servers are accessible over HTTP (port 80).
- The private IP addresses of the web servers are known.

## **Steps to Configure Apache as a Load Balancer**


### **1. Install and Configure Apache Load Balancer**

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

![1](https://github.com/user-attachments/assets/0c1af954-d91e-462b-90b4-ffa6bcf3fc33)


Restart the Apache service to apply changes:
```bash
sudo systemctl restart apache2
```

![2](https://github.com/user-attachments/assets/d252fc41-a168-4ce5-8b72-8e5d904da068)


#### **Verify Apache Installation**
Check if the Apache service is running:
```bash
sudo systemctl status apache2
```
You should see a message indicating that **apache2** is active and running.


### **2. Configure Load Balancing**

#### **Modify Apache Virtual Host Configuration**

Edit the default Apache virtual host configuration file:
```bash
sudo vi /etc/apache2/sites-available/000-default.conf
```
![3 vi](https://github.com/user-attachments/assets/0d23d302-4776-4ff4-8f09-ebdc71c6f48c)


Inside the `<VirtualHost *:80>` block, add the following configuration to define your load balancer:

```apache
<Proxy "balancer://mycluster">
    BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
    BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
    ProxySet lbmethod=bytraffic
</Proxy>

ProxyPreserveHost On
ProxyPass / balancer://mycluster/
ProxyPassReverse / balancer://mycluster/
```

![4 cat](https://github.com/user-attachments/assets/6b1f5384-f77e-4a17-957d-40eedcb67cd6)

- Replace `<WebServer1-Private-IP-Address>` and `<WebServer2-Private-IP-Address>` with the actual private IP addresses of your two web servers.
- **loadfactor** defines how much traffic each server gets. A loadfactor of 5 for both servers means the traffic will be distributed evenly.
- **lbmethod=bytraffic** distributes load based on the amount of traffic being handled by each server.

#### **Restart Apache**
After saving the configuration, restart Apache for the changes to take effect:
```bash
sudo systemctl restart apache2
```


### **4. Testing the Load Balancer**

#### **Access the Load Balancer**

In your browser, visit the **public IP address** or **DNS name** of the Apache load balancer:
```bash
http://<Load-Balancer-Public-IP-Address>/index.php
```

This will route the traffic through the load balancer to one of the web servers. Since both web servers are configured with the same load factor, the traffic should be evenly distributed.


### **5. Verifying Load Balancer Functionality**

#### **Monitor Web Server Logs**

To ensure the load balancer is routing traffic to both web servers, open SSH sessions for both web servers and run the following command on each to monitor the Apache access logs:
```bash
sudo tail -f /var/log/apache2/access.log
```

Now, refresh the page in your browser several times. You should see new log entries appear in both servers' logs as they receive the HTTP GET requests from the load balancer. The number of requests should be roughly equal since the load factor is the same for both servers.

#### **Log Output Example**
You should see output similar to this in both web server logs:
```
<client IP> - - [Date/Time] "GET /index.php HTTP/1.1" 200 -
```

This confirms that the load balancer is functioning correctly, and the traffic is distributed across both servers.


In the Apache configuration, you can try different load balancing algorithms by modifying the `lbmethod` parameter:

- **byrequests**: Balances traffic by the number of requests each server handles.
- **bybusyness**: Sends traffic to the server with the fewest open connections.
- **heartbeat**: Balances traffic based on the health status of each server.

To try these methods, replace `bytraffic` in the configuration with one of the other algorithms, restart Apache, and observe the behavior.
