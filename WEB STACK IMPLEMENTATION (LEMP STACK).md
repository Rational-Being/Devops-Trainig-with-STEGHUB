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


## Step 2: Install Nginx Web Server

Nginx is one of the most popular open-source web servers, ideal for hosting websites and web applications.

1. Install Nginx  using the package manager:
   
     ```
     sudo apt install nginx
     ```
   
2. Once installed, start the nginx service:
   
     ```
     sudo systemctl start nginx
     ```
   
#### **Allow Nginx Through the Firewall**
Ensure that Nginx is allowed through the firewall so it can serve web traffic.
##### But since I have apache runing on this server, I will configure nginx to run on port 8080

1. Allow HTTP traffic:
   
     ```
     sudo ufw allow 8080
     ```
2. Also, modify Nginx Default Configuration: Edit the Nginx configuration file to change the listening port from 80 to 8080:
    ```
    sudo vim /etc/nginx/sites-available/default
    ```

3. The modified server block should look like this:
```
  server {
    listen 8080;
    server_name localhost;

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
      }
  }
```
Finaly, restart Nginx: After making these changes, restart Nginx for the configuration to take effect:

    ```
    sudo systemctl restart nginx
    ```

#### Checking Nginx Service Status
Verify that nginx is running and enabled to start on boot:

1. Check the status:
   
     ```
     sudo systemctl status nginx
     ```

2. To ensure Nginx starts on system reboot:
   
     ```
     sudo systemctl enable nginx
     ```
  ![nginx status](https://github.com/user-attachments/assets/da7203fe-ce99-4fd1-a08f-7aa7cd1f1d5e)

![nginx info](https://github.com/user-attachments/assets/5ca2d4d3-f901-4cf3-b7af-adf9bfbf9f18)

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


### **Step 4: Install PHP-FPM (for Nginx to Process PHP Files)**

PHP is a popular server-side scripting language used for web development. To run dynamic content, one will need PHP installed alongside Apache and MySQL.

1. Install PHP and its basic dependencies:
     ```
     sudo apt install php-fpm
     ```
   
   This installs PHP and the Nginx PHP module, allowing Nginx to process PHP files.

2. After installing these extensions, restart Nginx:
   
     ```
     sudo systemctl restart Nginx
     ```
   

## Step 5: Configuring Nginx to Use PHP Processor

#####  Create Directory for Website Files
1. Create a directory for your website files:
   ```
   sudo mkdir -p /var/www/projectlemp
   ```

2. For security and permission management, assign ownership of the directory to the current user (usually the one you created in Step 1):
   ```
   sudo chown -R [username]:[username] /var/www/projectlemp
   ```

##### **3.4.2 Set Directory Permissions**
To ensure Apache can read the files properly, set the following permissions:

1. Set directory permissions:
   ```
   sudo chmod -R 755 /var/www/projectlemp
   ```

##### Edit it the configuration file in Nginx’s sites-available (the same file we edited in step 3)

1. Create a new virtual host configuration file:
   
     ```
     sudo vim /etc/nginx/sites-available/projectLEMP
     ```

   
2. Add the following configuration to the file
   ```
  server {
    listen 8080;
    server_name localhost;

    root /var/www/html;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php-fpm.sock;
    }

    location ~ /\.ht {
        deny all;
      }
  }

   ```

3. Activate your configuration by linking to the config file from Nginx’s sites-enabled directory:
  ```
 sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
  ```
This will tell Nginx to use the configuration next time it is reloaded.

To confirm theres is not problem run:
  ```
 sudo nginx -t
  ```

4. We also need to disable default Nginx host that is currently configured to listen on port 80, for this run:
  ```
  sudo unlink /etc/nginx/sites-enabled/default
  ```

5. Then reload Nginx to apply the changes:
  ```
 sudo systemctl reload nginx
  ```
Your new website is now active


### **Step 6: Testing the LEMP Stack**

#### **Creating a Sample PHP Info File**
To test the LEMP stack setup, create a PHP file that displays information about the PHP configuration.

Create a new PHP file:
   ```
   sudo vim /var/www/projectLEMP/index.php
   ```

2. Add the following code:
   ```php
   <php phpinfo();>
   ```

3. Save and close the file.




## Alternatively:


1. Create a new PHP file:
   ```
   sudo vim /var/www/projectLEMP/lemp.html
   ```

![lemp](https://github.com/user-attachments/assets/ac78ae7e-551c-491f-bc76-301c1e126842)

   
