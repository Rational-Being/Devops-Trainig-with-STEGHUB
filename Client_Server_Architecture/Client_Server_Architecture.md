### **Understanding Client-Server Architecture**

Client-server architecture is a fundamental concept in computer networks and systems that underpins how most modern applications, services, and networks are structured. This model divides computing tasks between **clients** and **servers**, each with distinct roles. Here’s a breakdown of the core ideas after refreshing my understanding:

#### **1. What is Client-Server Architecture?**

In a **client-server architecture**, two main entities interact:

- **Client**: The client is typically a user's device (e.g., a web browser, mobile app, desktop application) that makes requests to a server. Clients are responsible for the **frontend** user interface and sending requests to the server for data or services.
  
- **Server**: The server is a powerful system that processes and responds to client requests. It handles data storage, business logic, and resources that clients need. Servers manage the **backend**, responding to requests and sending data back to the client.

This model allows for the distribution of workload, where the client handles user interactions, and the server deals with data storage, processing, and security.

#### **2. How It Works: Request-Response Cycle**

In this architecture, communication between the client and server follows a **request-response** pattern:

1. **Client Request**: The client sends a request to the server. This can be for data (like requesting a webpage) or to perform an action (like submitting a form). This request typically uses a protocol like **HTTP** or **HTTPS** for web-based communication.
   
2. **Server Processing**: Upon receiving the request, the server processes it. It might involve querying a database, running computations, or validating inputs. The server then prepares the necessary response.
   
3. **Server Response**: The server sends a response back to the client. This can be the requested data (like HTML or JSON) or a status code indicating the success or failure of the request.

#### **3. Key Components in Client-Server Architecture**

- **Frontend (Client Side)**: The client typically handles rendering the user interface. Technologies like **HTML**, **CSS**, and **JavaScript** are used to create the presentation layer in web apps. In desktop apps, this might be a native interface, while in mobile apps, it could be frameworks like **React Native** or **Flutter**.
  
- **Backend (Server Side)**: The server side includes the application logic, databases, APIs, and resource management. Popular back-end frameworks include **Node.js** (JavaScript), **Django** (Python), **Laravel** (PHP), **Express** (JavaScript), and many others.

- **Database**: Often, the server interacts with a **database** (e.g., **MySQL**, **MongoDB**, **PostgreSQL**) to store and retrieve persistent data like user information, content, or application state.

- **Protocols**: Communication between clients and servers happens using specific protocols. The most common in web apps are **HTTP/HTTPS**. Other protocols like **FTP** (File Transfer Protocol) or **SMTP** (Simple Mail Transfer Protocol) might be used depending on the service.

---

The **client-server architecture** forms the backbone of most web and networked applications today. Understanding how the client makes requests and how the server processes and responds is critical for anyone working in deveops engineering, software development, network management, or cybersecurity. This knowledge is essential for designing efficient, scalable, and secure systems, as it emphasizes clear separation between user-facing and server-side responsibilities. 

### Implementing a Client-Server Architecture Using MySQL DBMS

To implement a **client-server architecture** using **MySQL DBMS** across two Linux-based virtual servers, the following steps outline the detailed process:

### **Steps for Client-Server Architecture Using MySQL RDBMS**

### **1. Create and Configure Two Linux-Based Virtual Servers**

We will name the servers as follows:

- **Server A**: `mysql-server` (This will host the MySQL Server)
- **Server B**: `mysql-client` (This will host the MySQL Client)

If you’re using virtual machines, set them up with any Linux distribution (e.g., Ubuntu). For this task, I’ll assume both servers are on the same local network and have IP addresses that can communicate with each other.

**Sample IP Configuration**:
- `mysql-server`: `192.168.1.10`
- `mysql-client`: `192.168.1.11`

### **2. Install MySQL Server on `mysql-server`**

Login to the `mysql-server` machine and perform the following:

1. **Update Package Lists**:
   ```bash
   sudo apt update
   ```

2. **Install MySQL Server**:
   ```bash
   sudo apt install mysql-server
   ```

3. **Verify MySQL Server Installation**:
   ```bash
   sudo systemctl status mysql
   ```
   Ensure the service is active. If it’s not running, start the MySQL server:
   ```bash
   sudo systemctl start mysql
   ```
![01 mongodb status](https://github.com/user-attachments/assets/941457d4-645d-4e8a-ab3b-376fbac36ce8)

---

### **3. Install MySQL Client on `mysql-client`**

Login to the `mysql-client` machine and perform the following:

1. **Update Package Lists**:
   ```bash
   sudo apt update
   ```

2. **Install MySQL Client**:
   ```bash
   sudo apt-get install default-mysql-client
      ```

3. **Verify MySQL Client Installation**:
   You can verify the installation using:
   ```bash
   mysql --version
   ```

---

### **4. Configure MySQL Server to Allow Remote Connections**

By default, MySQL only allows connections from `localhost` (i.e., from the server itself). We need to configure MySQL to accept remote connections.

#### **Step 1: Allow MySQL to Listen on All IP Addresses**
1. **Edit the MySQL configuration file** on `mysql-server`:
   ```bash
   sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
   ```

2. **Find the line** that starts with `bind-address`, change it to the server's IP address (e.g., `192.168.1.10`) or `0.0.0.0` to allow connections from any IP.

   ```bash
   bind-address = 0.0.0.0
   ```

3. **Save and exit** the file, then restart the MySQL service:
   ```bash
   sudo systemctl restart mysql
   ```

#### **Step 2: Create a MySQL User for Remote Access**
1. **Login to MySQL** on the `mysql-server` machine:
   ```bash
   sudo mysql -u root -p
   ```

2. **Create a new user** that will connect from the `mysql-client` machine. Replace `user_name`, `password`, and `client_IP` as appropriate (e.g., IP `192.168.1.11`):
   ```sql
   CREATE USER 'user_name'@'192.168.1.11' IDENTIFIED BY 'password';
   ```

3. **Grant privileges** to the newly created user:
   ```sql
   GRANT ALL PRIVILEGES ON *.* TO 'user_name'@'192.168.1.11';
   ```

4. **Flush privileges** to apply the changes:
   ```sql
   FLUSH PRIVILEGES;
   ```
![03 create user](https://github.com/user-attachments/assets/5f3c00d6-8441-4590-b284-6b6dbec5942b)

   
### **Open MySQL Port (3306) on the MySQL Server**

1. **Check if UFW is Installed**:
   If you're using **UFW (Uncomplicated Firewall)**, you can check if it’s enabled:
   ```bash
   sudo ufw status
   ```

2. **Allow MySQL Port**:
   If UFW is running, you need to allow incoming connections on port 3306. Run the following command:
   ```bash
   sudo ufw allow 3306/tcp
   ```

3. **Enable UFW (if not enabled)**:
   If UFW was not previously enabled, you can enable it with:
   ```bash
   sudo ufw enable
   ```

4. **Verify Firewall Rules**:
   After allowing the port, verify that the rules are set correctly:
   ```bash
   sudo ufw status
   ```
   You should see a line indicating that port 3306 is allowed.
---

### **5. Connect Remotely from `mysql-client` to `mysql-server`**

Now, on `mysql-client`, use the `mysql` utility to connect to the `mysql-server`.

1. **Run the following command** from the `mysql-client`:
   ```bash
   mysql -h 192.168.1.10 -u user_name -p
   ```

   Replace:
   - `192.168.1.10` with the IP address of the `mysql-server`.
   - `user_name` with the username you created in the previous step.

2. **Enter the password** when prompted.

---

### **6. Verify the Connection**

Once connected, you should be in the MySQL prompt on `mysql-client`. You can perform basic SQL queries to check the connection:

1. **Show available databases**:
   ```sql
   SHOW DATABASES;
   ```
![02 connected](https://github.com/user-attachments/assets/a20b57b2-eddc-4660-acd6-80eda004fde2)
2. **Create a test database**:
   ```sql
   CREATE DATABASE test_db;
   ```

3. **Use the new database**:
   ```sql
   USE test_db;
   ```

4. **Create a test table**:
   ```sql
   CREATE TABLE users (
       id INT AUTO_INCREMENT PRIMARY KEY,
       name VARCHAR(50),
       email VARCHAR(100)
   );
   ```

5. **Insert some data**:
   ```sql
   INSERT INTO users (name, email) VALUES ('Alice', 'alice@example.com');
   ```

6. **Retrieve the data**:
   ```sql
   SELECT * FROM users;
   ```

If you can see the data, this confirms that the `mysql-client` successfully connected to the `mysql-server` and can perform SQL queries and you have successfully implemented a basic **client-server architecture** using MySQL on two separate Linux virtual servers. The **mysql-server** hosts the MySQL database, and the **mysql-client** connects remotely to the server, allowing you to execute SQL queries.
