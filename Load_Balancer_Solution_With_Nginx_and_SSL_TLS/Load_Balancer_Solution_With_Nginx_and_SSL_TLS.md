

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
   sudo nano /etc/nginx/conf.d/load_balancer.conf
   ```
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

This configuration defines an upstream group of servers (backend) and a server block and distributes incoming traffic to the upstream servers using the `proxy_pass` directive.

## Step 3: Test Nginx Load Balancer

To test the Nginx load balancer, follow these steps:

1. Restart Nginx:
   ```bash
   sudo service nginx restart
   ```
2. Verify that Nginx is distributing traffic to the upstream servers:
   ```bash
   curl http://localhost
   ```




# Part 2: Register a New Domain Name and Configure Secured Connection using SSL/TLS Certificates

## Step 1: Register a New Domain Name

1. Register a new domain name with a registrar.
2. Verify domain name registration.

## Step 2: Install Certbot

1. Install Certbot on the server.
2. Verify Certbot installation.

## Step 3: Obtain SSL/TLS Certificate from Let's Encrypt

1. Use Certbot to obtain an SSL/TLS certificate from Let's Encrypt.
2. Verify certificate issuance.

## Step 4: Configure Nginx to use SSL/TLS Certificate

1. Update Nginx configuration to use the obtained SSL/TLS certificate.
2. Verify that Nginx is serving the website over HTTPS.

## Step 5: Test Secured Connection

1. Verify that the website is accessible over HTTPS.
2. Test SSL/TLS certificate validity and configuration.

---

# III. Part 2: Register a New Domain Name and Configure Secured Connection using SSL/TLS Certificates

## Step 1: Register a New Domain Name

To register a new domain name, follow these steps:

1. Go to a domain registrar (e.g., GoDaddy, Namecheap) and register a new domain name.
2. Verify domain name registration by checking the domain registrar's control panel or using a WHOIS lookup tool.

## Step 2: Install Certbot

To install Certbot on the server, follow these steps:

1. Install Certbot:
   ```bash
   sudo apt install certbot
   ```
2. Verify Certbot installation:
   ```bash
   certbot --version
   ```

## Step 3: Obtain SSL/TLS Certificate from Let's Encrypt

To obtain an SSL/TLS certificate from Let's Encrypt using Certbot, follow these steps:

1. Run the following command to obtain an SSL/TLS certificate:
   ```bash
   sudo certbot certonly --webroot --webroot-path=/var/www/html --email your_email@example.com --agree-tos --non-interactive --expand --domains -d example.com,www.example.com
   ```
2. Verify certificate issuance:
   ```bash
   sudo certbot certificates
   ```

## Step 4: Configure Nginx to use SSL/TLS Certificate

To configure Nginx to use the obtained SSL/TLS certificate, follow these steps:

1. Update the Nginx configuration file:
   ```bash
   sudo nano /etc/nginx/conf.d/load_balancer.conf
   ```
2. Add the following configuration:
   ```nginx
   server {

       listen 443 ssl;

       server_name example.com www.example.com;

       ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;

       ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

       ...

   }
   ```

This configuration enables SSL/TLS encryption on port 443 and specifies the SSL/TLS certificate and private key files obtained from Let's Encrypt.

3. Verify that Nginx is serving the website over HTTPS:
   ```bash
   curl https://example.com
   ```

## Step 5: Test Secured Connection

To test the secured connection, follow these steps:

1. Verify that the website is accessible over HTTPS:
   ```bash
   curl https://example.com
   ```
2. Test SSL/TLS certificate validity and configuration using tools like SSL Labs or Why No HTTPS.
