### Self-Study Note: Network Storage Concepts and Cloud Storage Types

Today, I took some time to dive deep into **network storage solutions** like NAS, SAN, and the associated protocols like NFS, SMB, iSCSI, and FTP. I also explored the differences between **Block-level storage** and **Object storage**, with a focus on how they’re utilized by **cloud service providers** like AWS.


#### Network-Attached Storage (NAS)
NAS is essentially a dedicated storage device that operates on a network, allowing multiple users and client devices to access data centrally. It works at the **file level**, and it’s easy to manage and expand. It’s ideal for environments where file sharing is critical, such as home networks or small businesses. NAS devices use protocols like **NFS (Network File System)** and **SMB (Server Message Block)** to facilitate file sharing across different operating systems.

#### Storage Area Network (SAN)
In contrast, SAN operates at the **block level** and is designed for high-performance environments, typically within data centers. SAN is not directly accessed via traditional network protocols (like NFS or SMB); instead, it connects storage devices to servers using technologies such as **iSCSI (Internet Small Computer Systems Interface)** or **Fibre Channel**. SAN provides faster access speeds compared to NAS but is more complex to set up and manage.


#### Storage Protocols I Studied:
- **NFS (Network File System):** A protocol used by NAS systems to share files over a network. Primarily used in Unix/Linux environments, it allows users to mount remote file systems as if they were local directories.
  
  - **SMB (Server Message Block):** Often used in Windows environments, SMB is another protocol for sharing files, printers, and serial ports over a network. It's common in NAS devices designed for Windows networks.
    
    - **(S)FTP (File Transfer Protocol):** A protocol used for transferring files over the internet or a network. **SFTP** adds security features like encryption to traditional FTP.

    - **iSCSI (Internet Small Computer Systems Interface):** This protocol allows block-level data storage to be shared over IP networks, which is critical for SAN systems.


    #### Block-level Storage vs. Object Storage
    - **Block-level storage**: This divides data into fixed-size blocks and stores them separately. Each block has a unique address, and the system reassembles the blocks when data is requested. **SAN** typically uses block-level storage, and it's suitable for databases or high-performance applications that require low-latency data access. Cloud providers, like AWS, offer **Amazon Elastic Block Store (EBS)** as their block storage solution, which is designed for low-latency access and is often used with EC2 instances.

    - **Object storage**: In contrast, this stores data as objects that include the data itself, metadata, and a unique identifier. Object storage is highly scalable and suitable for unstructured data, such as media files or backups. AWS provides **Amazon S3 (Simple Storage Service)** as an object storage service. Unlike block storage, which is often tied to a particular server or virtual machine, object storage is more abstracted and typically accessed via APIs.

    #### Network File System (NFS) in Cloud
    I also looked at how **NFS** is used in cloud environments. AWS offers **Amazon EFS (Elastic File System)**, a fully managed NFS file system that can be mounted by multiple EC2 instances. It provides scalable storage for workloads that require shared access to file systems across many servers.


    #### Key Takeaways:
    - **NAS** is easy to deploy and works great for file sharing (file-level access), while **SAN** provides more advanced, high-performance, block-level storage.
    - **NFS** and **SMB** are primarily used in NAS systems, while **iSCSI** is often used in SANs for block-level storage.
    - **Block-level storage** (like AWS EBS) is ideal for applications requiring low latency, such as databases, while **object storage** (like AWS S3) is best for storing unstructured data and is designed for massive scalability.
    - In AWS, I can use **EFS** (Network File System) for shared file access, **EBS** for block storage with VMs, and **S3** for scalable object storage.

    This study helped solidify my understanding of how storage technologies differ and how cloud services leverage these concepts to provide scalable, efficient storage solutions. I’m particularly excited to dive deeper into AWS EBS, EFS, and S3 to experiment with these storage types in real-world applications.
