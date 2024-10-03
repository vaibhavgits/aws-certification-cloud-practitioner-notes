# 💾 EC2 Instance Storage

- [EBS Elastic Block Store - Volumes](#ebs-elastic-block-store---volumes)
- [EBS Snapshots](#EBS-Snapshots)
- [EC2 Instance Store](#EC2-Instance-Store)
- [EFS Elastic File System](#EFS---Elastic-File-System)
- [EBS vs. EFS](#EBS-vs-EFS)
- [Amazon FSx](#amazon-fsx)
- [EC2 Storage Shared Responsibility Model](#EC2-Storage-Shared-Responsibility-Model)
- [Summary](#Summary)

Amazon EC2 provides you with these storage options which can be used independently or in combination to suit your requirements:-

## EBS Elastic Block Store - Volumes

**Main Information about Elastic Block Store**:

- Analogy - A USB drive that can be attached to any instance via network.
- An EBS Volume is a **network drive** that you can attach to your instance while they run. It uses the network to communicate with the instance, so it might have a little bit of latency.
- It can be detached from an instance and attached to other quickly (benefit of network driver)
- It Can persist data even if the instance is terminated.
- Can be mounted to one instance at a time (For Cloud Practitioner level exam, For Associate level, multi-attach feature is available for some EBS). - One instance can have more than one EBS at a time.
- They are bound to a specific availability zone, this means if your instance is in us-east1-a and your EBS is in us-east1-b, they cannot be bound to each other. To do it across AZs, we need **Snapshots** or create an EBS Volume to the same AZ.
- It have a provisioned capacity in GBs or IOPs (inputs and outputs per second). The billing is based on this capacity, and this capacity can be increased/decreased over time.

**Good to know about EBS**:

- Free tier: 30 GB of EBS on General Purpose (SSD) or Magnetic per Month.
- Analogy to a "network USB stick" that you can plug in other computers.
- We can have existing EBS volumes that keeps unattached.
- EBS Volumes are network drives with good but 'limited' performance: EC2 Instance Store have the best performance, because they are the hard disk, physical, on the data-center attached on the machine that runs the server.

> Amazon Elastic Block Store (EBS) is an easy to use, high-performance block storage service designed for use with Amazon Elastic Compute Cloud (EC2) for both throughput and transaction-intensive workloads at any scale. A broad range of workloads, such as relational and non-relational databases, enterprise applications, containerized applications, big data analytics engines, file systems, and media workflows are widely deployed on Amazon EBS.

**EBS - Delete on Termination**

  - By default, If an instance is terminated, the root EBS volume is also deleted.
  - By default, any other EBS volume is not deleted.

<p align="center" width="100%"><img src="assets/ebs.jpg" alt="ebs" width="400"/></p>

---

## EBS Snapshots

Snapshot is a backup of our EBS Volumes at a point in time. It keeps available in one Region. But to use it in a volume, we need to create the volume inside an AZ.

- To Create this snapshot you don't have to detach the volume from an instance (besides it is recommended)
- With the Snapshot we can Copy to other regions or AZ's or Create a brand new EBS Volume.
- This snapshot will be a "start" to the new EBS Volume. So the new volume will start with the data starting from the snapshot. The new volume will be a Restore from a snapshot.
- Amazon EBS Snapshots are stored incrementally, which means you are billed only for the changed blocks stored

**EBS Snapshots - Features**

- EBS Snapshot Archives - Movies a snapshot to an "archive tier" that is 75% cheaper. Takes 24 hours to 72 hours for restoring the archive.
- Recycle Bin for EBS Snapshots - Setup rules to retain deleted snapshots so you can recover them after an accidental deletion. It has specific retention duration - from 1 day to 1 year , which depends on the user. 

<p align="center" width="100%"><img src="assets/ebs-snapshots.jpg" alt="ebs-snapshots" width="400"/></p>

---

## AMI - Amazon Machine Image

- AMI are a customization of an EC2 instance
 - You add your own software, configuration, operating system, monitoring etc.
 - Faster boot / configuration time because all your software is pre-packaged
- AMI are built for a specific region (and can be copied across regions)
- You can launch EC2 instances from:
 - A Public AMI: AWS provided
 - Your own AMI: you make and maintain them yourself
 - An AWS Marketplace AMI: an AMI someone else made (and potentially sells)

**AMI Process**

- Start an EC2 instance and customize it
- Stop the instance (for data integrity)
- Build an AMI — this will also create EBS snapshots
- Launch instances from other AMIs

![AMI Process](https://github.com/user-attachments/assets/2aab4615-93df-4cbb-bf5b-1ed48943f3c1)

---

## EC2 Image Builder

- EC2 Image Builder is used to automate the creation of VM or Container images.
- It automates the creation, maintain, validate and test EC2 AMIs.
- It can be run on a schedule e.g. weekly, when a package is updated etc.
- It's a Free Service. Only pay for underlying resurces. 

**How EC2 works in detail?**

- EC2 Image Builder service automatically creates an EC2 instance called "Builder EC2 instance". This instance will perform tasks you defined on it such as installation of a certain softwares like java, updating packages etc. 
- Once this is done, an AMI is created out of this EC2 instance.
- Now EC2 Instance will create a "Test EC2 isntance" from the AMI to run certain test that you will define like security etc. You can also skip the "tests".
- After this, AMI is distributed. 

![Image Builder](https://github.com/user-attachments/assets/e4b02d20-569a-41a8-96e1-3f2eb1e9edd5)

---

## EC2 Instance Store

This is the **physical HARD DRIVE** attached to the server. Limited space, but higher performance. It is called by **ephemeral**

- Better I/O performance.
- It is good to buffer/cache/scratch data/temp files. Always with short workloads.
- EC2 Instance Store lose their storage and data if they are stopped.
- Risk of losing data if hardware fails(because it is a physical hardware). 
- Backups and Replications are our responsibility.
- It is Block Level storage.

> An instance store provides temporary block-level storage for your EC2 instance. This storage is located on disks that are physically attached to the host computer. Instance store is ideal for the temporary storage of information that changes frequently, such as buffers, caches, scratch data, and other temporary content, or for data that is replicated across a fleet of instances, such as a load-balanced pool of web servers. Instance storage is temporary, data is lost if instance experiences failure or is terminated. EC2 instance store cannot be used for file sharing between instances.

---

## EFS - Elastic File System

EFS stands to Elastic File System and it is a Network File System (NFS). This NFS can be attached to hundreds of EC2 instances at a time which makes it a shared NFS.

- Amazon EFS is a regional service storing data within and across multiple Availability Zones (AZs) for high availability and durability.
- It is a shared NFS.
- Works in Linux EC2 Instances and works across multiple AZs. Amazon EFS is a regional service, meaning it works across multiple Availability Zones within the same AWS region. This means you can have EC2 instances in different AZs (e.g., one in AZ-A and another in AZ-B) and both can be attached to the same EFS file system. This allows for shared file storage across instances in different AZs, which is useful for highly available, scalable applications.
- It makes EFS High Available and Scalable. But, this also makes the EFS more expensive (3x gp2) and you pay what you use  and not the capacity. If you use only 20gb, that's your usage and you'll pay for this.
- High Available By Default
- Data access: EC2 instances can access files on an EFS file system across many Availability Zones, Regions and VPCs
  - Amazon EC2 instances can access your file system across AZs, regions, and VPCs
  - On-premises servers can access using AWS Direct Connect or AWS VPN.

![EFS](https://github.com/user-attachments/assets/e17274f4-9349-4625-b997-0861abf6db2d)


**EBS vs EFS**

**EBS (Elastic Block Store):** AZ-specific: An EBS volume is tied to a specific Availability Zone (AZ). So, if your EC2 instance is in AZ1, the EBS volume must also be in AZ1.
- Cross-AZ migration: If you want to move or use the EBS volume in AZ2, you first need to create an EBS Snapshot (a backup of the volume), and then restore it to create a new EBS volume in AZ2.

**EFS (Elastic File System):** Multi-AZ access: EFS is a regional service, meaning it works across multiple AZs. EC2 instances from different AZs (e.g., x instances in AZ1 and y instances in AZ2) can all access the same EFS file system.
- EFS Mount Target: To allow EC2 instances in different AZs to access EFS, you create an EFS Mount Target in each AZ. These mount targets act as connection points for the instances in each AZ to access the EFS file system.

![EBS vs EFS](https://github.com/user-attachments/assets/bbd29efe-44bc-4b3d-a502-761d1cad72e5)

> Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed, elastic NFS file system. It is built to scale on-demand to petabytes without disrupting applications, growing and shrinking automatically as you add and remove files, eliminating the need to provision and manage capacity to accommodate growth. Amazon EFS is designed to provide massively parallel shared access to thousands of Amazon EC2 instances, enabling your applications to achieve high levels of aggregate throughput and IOPS with consistent low latencies.

<p align="center" width="100%"><img src="assets/efs.jpg" alt="efs" width="300"/></p>

---

**EFS Storage Options**

- Amazon EFS Standard Storage Class
  - The EFS Standard storage class is designed for active file system workloads, and you pay only for the amount of file system storage you use per month. Data is stored regionally within and across multiple Availability Zones (AZs).
    
- Amazon EFS Standard-Infrequent Access Storage Class:
  - The EFS Standard-Infrequent Access storage class (EFS Standard-IA) is cost-optimized for files accessed less frequently. Data stored on the EFS Standard-IA storage class costs less than EFS Standard storage class, and you will pay a fee each time you read from or write to a file. Data is stored regionally within and across multiple Availability Zones (AZs).
  - Enable EFS-IA with a Lifecycle Policy. e.g. Move files that are not accessed for 60 days to EFS-IA.
  - When you enable EFS-IA, EFS will automatically move your files to EFS-IA based on the last time they were accessed. 

![EFS IA](https://github.com/user-attachments/assets/6fefb9df-2e1a-4e68-9498-5378e88961d5)


- Amazon EFS One Zone Storage Class:
  - The EFS One Zone storage class is designed for active file system workloads, and you pay only for the amount of file system storage you use per month. Data is stored within a single Availability Zone. Standard data transfer fees apply for inter-AZ or inter-region access to file systems.

- Amazon EFS One Zone-Infrequent Access Storage Class:
  - The EFS One Zone-Infrequent Access storage class (EFS One Zone-IA) is cost-optimized for files accessed less frequently. Data stored on the EFS One Zone-IA storage class costs less than the One Zone storage class, and you will pay a fee each time you read from or write to a file. Data is stored within a single Availability Zone. Standard data transfer fees apply for inter-AZ or inter-region access to file systems.

- Amazon EFS Bursting Throughput (Default):
  - In the default Bursting Throughput mode, there are no charges for bandwidth or requests, and you get a baseline rate of 50 KB/s per GB of throughput included with the price of EFS Standard storage class. Read operations are metered at a 1:3 ratio toward this rate, so you can drive up to 150 KB/s per GB of read throughput or 50 KB/s per GB of write throughput with this baseline rate.

- Amazon EFS Provisioned Throughput:
  - You can optionally select the Provisioned Throughput mode and provision the throughput of your file system independent of the amount of data stored and pay separately for storage and throughput. Read operations are metered at a 1:3 ratio toward this rate, so you can drive up to 3 MB/s of read throughput or 1 MB/s of write throughput for every 1 MB/s of throughput provisioned. Like the default Bursting Throughput mode, the Provisioned Throughput mode also includes 50 KB/s per GB (or 1 MB/s per 20 GB) of throughput in the price of EFS Standard and EFS One Zone storage classes. You are billed only for the throughput provisioned above what you are provided based on data you have stored.

## Amazon FSx

We have two types of FSx in AWS:

**Amazon FSx for Windows File Server**:

- Fully managed, highly reliable and scalable windows native shared file systems.
- Built on windows file server and supports SMB Protocol (Server Message Block) and Windows NTFS (New Technology File System - windows file system)
- Integrated with Microsoft Active Directory
- Can be accessed from AWS or On-premise

<p align="center" width="100%"><img src="assets/fsx.jpg" alt="fsx" width="300"/></p>

**Amazon FSx for Lustre**:

- A fully managed, high-performance, scalable file storage for High Performance Computing (HPC)
- Works for Linux and Clusters (Lusters)
- Machine Learning, Analytics, Video Processing, Financial Modeling
- Scales up to 100s GB/s, millions of IOPS, sub-ms latencies

<p align="center" width="100%"><img src="assets/fsx-hpc.jpg" alt="fsx" width="400"/></p>

### EC2 Storage Shared Responsibility Model

**AWS Responsibility**

- Infrastructure
- Replication for Data through the Drivers and Volumes (ensure disasters recover or in case a Hardware stop working)
- Replace Faulty Hardwares
- Ensure that their employees cannot access our data.

**User Responsibility**

- Setup backup and snapshots procedures
- Setup Data encryption
- Responsibility about the data content on drivers
- Understand the risk of using [EC2 Instance Store](#EC2-Instance-Store)

## Summary

- EBS volumes:
  - Network drives attached to one EC2 instance at a time
  - Mapped to an Availability Zones
  - Can use EBS Snapshots for backups / transferring EBS volumes across AZ
- AMI: create ready-to-use EC2 instances with our customizations
  - EC2 Image Builder: automatically build, test and distribute AMIs
- EC2 Instance Store:
  - High performance hardware disk attached to our EC2 instance
  - Lost if our instance is stopped / terminated
- EFS: network file system, can be attached to 100s of instances in a region
- EFS-IA: cost-optimized storage class for infrequent accessed files
- FSx for Windows: Network File System for Windows servers
- FSx for Lustre: High Performance Computing (HPC) Linux file system
