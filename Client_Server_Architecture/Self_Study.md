i### **Self-Study Notes**

#### **1. Ping and Traceroute: Network Diagnostic Utilities**

I spent some time reading up on **ping** and **traceroute**, two essential network diagnostic tools, and now feel more confident interpreting their results.

- **Ping**: This tool is straightforward and uses **ICMP (Internet Control Message Protocol)** to check if a host is reachable. It sends packets to a destination and waits for a reply, measuring round-trip time and packet loss along the way. I learned that **ping** is great for testing basic connectivity between two devices. The command is simple:

    ```bash
    ping <destination>
    ```
![ping](https://github.com/user-attachments/assets/c1207e01-dab5-403a-ae4b-f4cd54da68d9)

    From the results, I can now easily interpret:
    
    - **Time (ms)**: The time it takes for the packet to travel from my machine to the destination and back.
    - **Packet Loss**: If there’s a loss in connectivity, this will indicate how many packets didn’t return.
    - **TTL (Time to Live)**: The maximum time the packet can travel. A lower TTL might indicate that the packet has been routed through many hops before reaching the destination.

- **Traceroute**: This tool provides a more detailed picture than **ping** by showing the path a packet takes from my machine to the destination, including all the intermediate hops. It’s incredibly useful for identifying where latency or packet loss occurs. The command I used:

    ```bash
    traceroute <destination>
    ```
    ![traceroutes](https://github.com/user-attachments/assets/a1168a89-55f4-42d8-8e8c-cd5051ef3d87)

    Key takeaways from the traceroute output:
    
    - Each line represents a **hop**, or a router that the packet passed through.
    - The **IP addresses** and hostnames of each hop show which networks the packet traversed.
    - The **response times** (measured three times per hop) reveal how long each hop took. High latency at a specific hop can help identify where slowdowns or network issues are occurring.

With **ping** and **traceroute**, I’m now much better equipped to troubleshoot network issues, detect bottlenecks, and understand the flow of traffic between different systemsi and networks.

---

#### **2. Refreshing My Knowledge of Basic SQL Commands**

Next, I brushed up on some fundamental **SQL** commands to make sure I’m comfortable with essential database operations. Here’s a quick rundown of what I reviewed:

- **SHOW**: This command is useful for displaying database information, such as existing databases or tables. For example, to list all databases, I would use:

    ```sql
    SHOW DATABASES;
    ```

- **CREATE**: I practiced creating databases and tables using **CREATE**. For example, creating a new table to store user information might look like this:

    ```sql
    CREATE TABLE users (
      id INT AUTO_INCREMENT PRIMARY KEY,
      name VARCHAR(50),
      email VARCHAR(100)
    );
    ```

- **DROP**: When I need to delete a database or table, I can use **DROP**. It's a destructive command, so I have to be careful! To delete a table, I would run:

    ```sql
    DROP TABLE users;
    ```

- **SELECT**: This is probably the most used SQL command, allowing me to retrieve data from tables. I practiced selecting specific columns from tables, like this:

    ```sql
    SELECT name, email FROM users;
    ```

- **INSERT**: I also refreshed my knowledge on how to insert data into tables. Here’s an example of how I would add a new user to the `users` table:

    ```sql
    INSERT INTO users (name, email) VALUES ('Rational Being', 'rb@example.com');
    ```

These commands are crucial when working with databases, whether I’m setting up new tables, querying data, or performing database maintenance.

---

