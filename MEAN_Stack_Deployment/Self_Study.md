### **Self-Study Notes**

#### **1. Refreshing My Knowledge of the OSI Model**

After revisiting the **OSI Model**, I was reminded of how crucial it is in understanding how networks function. The model breaks down communication into seven layers, making it easier to grasp how data flows across a network:

1. **Physical Layer**: This layer deals with the physical connection between devices, such as cables, switches, and voltage levels.
   
2. **Data Link Layer**: Here, devices communicate over the same physical medium (e.g., Ethernet). It handles MAC addressing and error detection.

3. **Network Layer**: This layer is responsible for routing packets from source to destination, with protocols like IP (Internet Protocol).

4. **Transport Layer**: This is where TCP/UDP come into play, ensuring reliable data transmission, flow control, and error correction.

5. **Session Layer**: Manages sessions or connections between applications, keeping them synchronized and handling session restoration in case of failure.

6. **Presentation Layer**: This layer ensures data is in a readable format, handling encryption, compression, and translation between data formats (e.g., JPEG, ASCII).

7. **Application Layer**: At the top, this layer interacts with the end user and provides services like HTTP, SMTP, and FTP.

I noticed that as a Devops Engineer, I mostly interact with layers 7 (Application), when deploying web applications.
---

#### **2. Load Balancing**

Diving into **Load Balancing**, I learned about its significance in distributing network or application traffic across multiple servers to ensure no single server is overwhelmed. This not only improves availability but also enhances reliability and reduces response time.

There are various types and techniques I explored:

- **Round Robin**: This method cycles through servers in a predefined order. It's simple but doesn’t consider current server load.
  
- **Least Connections**: This approach sends traffic to the server with the fewest active connections, ensuring more balanced distribution during heavy loads.
  
- **IP Hash**: A unique hash of the client’s IP address is used to consistently route requests from the same client to the same server. This is especially useful when maintaining session persistence.
  
- **Weighted Round Robin**: Here, each server is assigned a weight based on its capacity, allowing for a more intelligent distribution of traffic based on server resources.

Techniques like **Layer 4 (Transport Layer)** and **Layer 7 (Application Layer)** load balancing offer deeper control over traffic management. Layer 4 manages traffic at the transport level, while Layer 7 makes decisions based on HTTP headers or other application-specific data. Understanding these techniques helps me see how they can improve scalability, availability, and fault tolerance for web applications.

---

#### **3. HTML + CSS + JS: Editing Simple Web Forms**

I spent some time practicing simple web form creation and manipulation using **HTML**, **CSS**, and **JavaScript** while implemnting the MEAN stack. It was a great refresher on front-end fundamentals, and I focused on how these technologies work together to create interactive, user-friendly forms.

These exercises strengthened my ability to edit and customize web forms, which is essential when testing web apps or building user interfaces as part of development projects. 

---
