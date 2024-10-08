

## Brief Overview of the Project

This project involves registering a website with Let's Encrypt Certificate Authority and automating certificate issuance using Certbot. The project consists of two parts: configuring Nginx as a load balancer and registering a new domain name and configuring a secured connection using SSL/TTLS certificates.

### Importance of SSL/TLS Certificates and Load Balancing

SSL/TLS certificates are essential for website security as they enable encrypted communication between the website and its users. This encryption ensures that data exchanged between the website and its users remains confidential and cannot be intercepted or tampered with by unauthorized parties. Load balancing, on the other hand, is crucial for website performance as it distributes incoming traffic across multiple servers, ensuring that no single server is overwhelmed and becomes a single point of failure.

# Part 1: Configure Nginx as a Load Balancer
## Step 1: Install Nginx

To install Nginx on the server, follow these steps:

1. Open a terminal and update the package list:
   ```bash
   sudo apt update
   ```
2. Install Nginx:
   ```bash
   sudo apt install nginx
   ```

## Step 2: Configure Nginx as a Load Balancer

To configure Nginx as a load balancer, create a new Nginx configuration file:

1. Create a new file in the `/etc/nginx/conf.d/` directory:
   ```bash
   sudo vim /etc/nginx/conf.d/load_balancer.conf
   ```
![01 vim](https://github.com/user-attachments/assets/08ea5b0d-0141-4364-96b9-fa9e95ffde29)


2. Add the following configuration:
   ```nginx
     upstream myproject {
        server Web1 weight=5;
        server Web2 weight=5;
      }
    
    server {
        listen 80;
        server_name www.domain.com;
        location / {
          proxy_pass http://myproject;
        }
      }
    
    #comment out this line
    # include /etc/nginx/sites-enabled/*;
   ```

![02  vim conf](https://github.com/user-attachments/assets/51567752-f627-4ce5-b93c-c8a05e62ae61)

This configuration defines an upstream group of servers (backend) and a server block and distributes incoming traffic to the upstream servers using the `proxy_pass` directive.

## Step 3: Test Nginx Load Balancer

To test the Nginx load balancer, follow these steps:

   ```bash
   sudo systemctl restart nginx
   sudo systemctl status nginx  
   ```
![03 restart and status](https://github.com/user-attachments/assets/dc18f260-c0f1-4af7-a627-64925cae2af3)


# Part 2: Register a New Domain Name and Configure Secured Connection using SSL/TLS Certificates


1. Register a new domain name with a registrar, in my case, I have registered with https://get.tech/
2. Verify domain name registration.
   
 ![04 whois](https://github.com/user-attachments/assets/2f751368-e5c2-4f91-a07e-fda42018615d)

3. Update A record in your registrar to point to Nginx LB

![05 A record](https://github.com/user-attachments/assets/7996238b-0783-428a-855f-093047fa549f)

   
### Configuring Nginx and Securing Your Domain with SSL/TLS

Update your Nginx configuration to recognize your domain, securing the website with Let's Encrypt SSL/TLS certificates, and setting up automated certificate renewal.


## Step 1: Update Nginx Configuration for Your Domain

1. Open your Nginx configuration file (`nginx.conf`) or the relevant server block file.
   ```bash
   sudo vim /etc/nginx/nginx.conf
   ```
   
2. Update the `server_name` directive to reflect your domain:
   ```nginx
   server_name www.<your-domain-name.com>;
   ```
   Replace `<your-domain-name.com>` with your actual domain name.

![06 update sever name](https://github.com/user-attachments/assets/5538324c-a133-42c1-a6fc-75a734e828b4)


3. Save and exit the file, then reload Nginx to apply the changes:
   ```bash
   sudo systemctl reload nginx
   ```

---

## Step 2: Install Certbot and Request an SSL/TLS Certificate

### 2.1 Ensure `snapd` is Active

Certbot can be installed using Snap. First, ensure that the `snapd` service is running:
```bash
sudo systemctl status snapd
```

If `snapd` is not installed, you can install it using:
```bash
sudo apt update
sudo apt install snapd
```

### 2.2 Install Certbot

Once `snapd` is active, install Certbot:
```bash
sudo snap install --classic certbot
```

Create a symbolic link to ensure Certbot can be run from the command line:
```bash
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```

### 2.3 Request Your SSL/TLS Certificate

1. Run Certbot to automatically configure Nginx with SSL/TLS:
   ```bash
   sudo certbot --nginx
   ```
2. Follow the on-screen instructions to select the domain you want the certificate issued for. Certbot will look up the domain name from your Nginx configuration file (updated in **Step 1** above).

3. Once the process is complete, Certbot will configure Nginx to use the issued certificate and reload the service.

---

## Step 3: Test Secured Access to Your Website

After obtaining the SSL/TLS certificate, test access to your website over HTTPS:

1. Open your browser and visit:
   ```bash
   https://<your-domain-name.com>
   ```

2. You should see a **padlock** icon in the browser’s address bar, indicating that the connection is secured with SSL/TLS.

3. Click on the padlock to view details about the certificate issued to your site, including the issuer (Let's Encrypt) and expiration date.

---

## Step 4: Set Up Automatic SSL/TLS Certificate Renewal

### 4.1 Test Certificate Renewal in Dry Run Mode

Let's Encrypt certificates are valid for 90 days. To avoid downtime, it’s essential to renew the certificate regularly. You can test the renewal process with Certbot’s `--dry-run` option:
```bash
sudo certbot renew --dry-run
```

### 4.2 Schedule Automatic Renewal Using Cron

To automatically renew the certificate, set up a cron job that runs twice a day:

1. Open the crontab editor:
   ```bash
   sudo crontab -e
   ```

2. Add the following line to schedule renewal every 12 hours:
   ```bash
   0 */12 * * * /usr/bin/certbot renew > /dev/null 2>&1
   ```

   - This cron job will attempt to renew the certificate twice a day.
   - You can adjust the schedule expression as needed.

### 4.3 Verify Cron Job

After saving, verify the cron job is working by checking the system logs or running the command manually:
```bash
sudo certbot renew
```
