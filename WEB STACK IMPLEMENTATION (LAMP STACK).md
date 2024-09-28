## Step 0 - Preparing prerequisites

1. While the prerequisties for this learning is to use AWS EC2, I have not being able to make a successful registration on Amazon after so much struggle so I have used a VPS instead, I will repeat this exercise when I finally have access to the EC2 instance, so as to be familiar with the Amazon Environment.
2. SSH proctocol to establish connectivity

## Step 1: Connect to the VPS

The VPS provider is Contabo, 4 cores and 6gb ram, it runs on Ubuntu 22.04 LTS Operating System. I also ave the IP adress and credentials for SSH access.
I accessed the VPS thus:
     ```
     ssh alpha@IpAddress
     ```

###### **SSH Key Generation**
SSH keys provide a secure way to authenticate without using passwords. It is highly recommended to use **SSH key-based authentication** for increased security.

Then, I generated SSH Keys on my local machine
   ```
   ssh-keygen -t rsa -b 4096
   ```
   - This generates a 4096-bit RSA key. 


2. When prompted, I specified the location where I want to save the key pair (default location is `~/.ssh/id_rsa`):
  ```
   Enter file in which to save the key (/home/username/.ssh/steghub_rsa): 
   ```

3. When prompted to enter a **passphrase**, I skipped this option since it is not mandatory:
   ```
   Enter passphrase (empty for no passphrase): [Type a passphrase or press Enter]
   ```

4. Two files were created:
   - **Private Key**: `~/.ssh/steghub (keep this safe and never share it).
   - **Public Key**: `~/.ssh/steghub.pub` (this will be copied to the VPS).

##### **Copy SSH Keys to the Server**
To use the newly generated SSH key, I copied the **public** key to the VPS:

   On the VPS, I created the `~/.ssh` directory for the new user and set permissions:
      ```
      mkdir -p ~/.ssh
      chmod 700 ~/.ssh
      ```

   Then create the `authorized_keys` file, pasted my public key, and set the appropriate permissions:
      ```
      vim ~/.ssh/authorized_keys
      chmod 600 ~/.ssh/authorized_keys
      ```


#### **Configuring UFW (Uncomplicated Firewall)**
A firewall helps protect your VPS from unauthorized access. UFW is a simple firewall setup tool available on Ubuntu.

To install UFW (if it's not already installed):

```
sudo apt install ufw
```

##### **Allow SSH through UFW**
Since I'll be using SSH to manage my VPS, I ensured the firewall allows SSH connections.

1. Allow SSH on the default port (22):
   ```
   sudo ufw allow OpenSSH
   ```

##### **Enable and Status Check**
1. To enable UFW and start the firewall:
   ```
   sudo ufw enable
   ```

2. Check the status of UFW and ensure the rules are in place:
   ```
   sudo ufw status
   ```
![fire](https://github.com/user-attachments/assets/80491d71-9943-4707-a465-b46f5b3a2ef0)

Now my VPS is secured with SSH key-based authentication and has a firewall in place.

---

#### Updating Package Lists**
Before installing any software, it's a good practice to update the package list to ensure you're pulling the latest versions from the repositories.

 Run the following command:
   ```
   sudo apt update
   ```

####  Upgrading Installed Packages
Once the package list is updated, upgrade all currently installed packages:

1. Execute the upgrade command:
   ```
   sudo apt upgrade
   ```


## Step 2: Install Apache Web Server

Apache is one of the most popular open-source web servers, ideal for hosting websites and web applications.

1. Install Apache using the package manager:
   
     ```
     sudo apt install apache2
     ```
   
2. Once installed, start the Apache service:
   
     ```
     sudo systemctl start apache2
     ```
   
#### **Allow Apache Through the Firewall**
Ensure that Apache is allowed through the firewall so it can serve web traffic.

1. Allow HTTP and HTTPS traffic:
   
     ```
     sudo ufw allow 80
     ```

#### Checking Apache Service Status
Verify that Apache is running and enabled to start on boot:

1. Check the status:
   
     ```
     sudo systemctl status apache2
     ```

2. To ensure Apache starts on system reboot:
   
     ```
     sudo systemctl enable apache2
     ```
   ![apap](https://github.com/user-attachments/assets/8d887005-e42d-40b9-94f5-cc8c5f4072e0)


![info](https://github.com/user-attachments/assets/1004f15f-dd2d-465b-9083-957b8bf8ceee)

### **Step 3: Install MySQL Database**

####  Installing MySQL Server
MySQL is a popular relational database used for storing data in web applications.

1. Install MySQL:
   
     ```
     sudo apt install mysql-server
     ```
   
2. Start the MySQL service:
   
     ```
     sudo systemctl start mysql
     ```
   
3. Enable MySQL to start on boot:
   
     ```
     sudo systemctl enable mysql
     ```
   
#### **Securing MySQL Installation**
To enhance the security of the MySQL server, run the MySQL secure installation script:

1. Execute the security script:
   ```
   sudo mysql_secure_installation
   ```

2. Follow the prompts:
   - Set a strong root password.
   - Remove anonymous users.
   - Disallow root login remotely.
   - Remove the test database and access to it.
   - Reload privilege tables.

![sql](https://github.com/user-attachments/assets/3b4d433a-8e49-4650-8d4b-f5d5752a2a21)


##### To ensure the new user and database were created successfully:

1. Log into MySQL as the newly created user:
   ```
   sudo mysql -p
   ```

2. List the databases to verify the user has access to the correct database:
   ```sql
   SHOW DATABASES;
   ```
![mysql](https://github.com/user-attachments/assets/82a62132-44dc-4246-bb45-fd5557392467)


### **Step 4: Install PHP**

PHP is a popular server-side scripting language used for web development. To run dynamic content, one will need PHP installed alongside Apache and MySQL.

1. Install PHP and its basic dependencies:
     ```
     sudo apt install php libapache2-mod-php php-mysql
     ```
   
   This installs PHP and the Apache PHP module, allowing Apache to process PHP files.

2. Installing Common PHP Extensions
PHP extensions add extra functionality to your PHP environment. Here are some common ones required for most web applications.

1. Install commonly used PHP extensions:
   
     ```
     sudo apt install php-mysql php-xml php-gd php-curl php-zip
     ```    

2. After installing these extensions, restart Apache:
   
     ```
     sudo systemctl restart apache2
     ```
   
- **php-mysql**: Enables PHP to communicate with MySQL databases.
- **php-xml**: Provides XML parsing functionality.
- **php-gd**: Used for image manipulation.
- **php-curl**: Allows you to send HTTP requests.
- **php-zip**: Provides zip file handling.

![ppp](https://github.com/user-attachments/assets/3c5e86c5-53fd-41b1-b2fe-f3055a252373)




## Step 5: Creating a Virtual Host 

Virtual hosts allow you to host multiple websites on a single server.

#####  Create Directory for Website Files
1. Create a directory for your website files:
   ```
   sudo mkdir -p /var/www/projectlamp
   ```

2. For security and permission management, assign ownership of the directory to the current user (usually the one you created in Step 1):
   ```
   sudo chown -R [username]:[username] /var/www/projectlamp
   ```

##### **3.4.2 Set Directory Permissions**
To ensure Apache can read the files properly, set the following permissions:

1. Set directory permissions:
   ```
   sudo chmod -R 755 /var/www/projectlamp
   ```

##### **3.4.3 Create a Virtual Host File**
1. Create a new virtual host configuration file:
   
     ```
     sudo vim /etc/apache2/sites-available/projectlamp.conf
     ```
   
2. Add the following configuration to the file
   ```apache
   <VirtualHost *:80>
       ServerAdmin projectlamp
       ServerName www.projectlamp
       ServerAlias webmaster@localhost
       DocumentRoot /var/www/projectlamp
       ErrorLog ${APACHE_LOG_DIR}/error.log
       CustomLog ${APACHE_LOG_DIR}/access.log combined
   </VirtualHost>
   ```

3. Save and close the file.

##### **Enable the New Virtual Host**
1. Enable the new virtual host:
   
     ```
     sudo a2ensite projectlamp.conf
     ```

2. Test the configuration for any syntax errors:

     ```
     sudo apache2ctl configtest
     ```
   
3. Restart Apache to apply changes:
     ```
     sudo systemctl restart apache2
     ```
 

### **Step 6: Testing the LAMP Stack**

#### **Creating a Sample PHP Info File**
To test the LAMP stack setup, create a PHP file that displays information about the PHP configuration.

Create a new PHP file:
   ```
   sudo vim /var/www/projectlamp/index.php
   ```

2. Add the following code:
   ```php
   <php phpinfo();>
   ```

3. Save and close the file.

![ppin](https://github.com/user-attachments/assets/5f606c3d-086b-4d93-ba5e-1ff129d90bed)



## Alternatively:


1. Create a new PHP file:
   ```
   sudo vim /var/www/projectlamp/index.html
   ```

2. Add the following code:
   ![code](https://github.com/user-attachments/assets/33b1d65d-0ad0-4bcb-95e8-c6cea891d035)


![complete](https://github.com/user-attachments/assets/8d6a94e4-f1cc-43d4-8ae1-5649ed44e5e3)
