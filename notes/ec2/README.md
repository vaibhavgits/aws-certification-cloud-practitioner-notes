# 📝EC2 - Elastic Cloud Compute

- [EC2 Sizing & Configuration Options](#ec2-sizing-&-configuration-options)
- [EC2 User Data](#ec2-user-data)
- [How to Create a Basic EC2 Instance](#How-to-Create-a-Basic-EC2-Instance)
- [Amazon Machine Image](#Amazon-Machine-Image)
- [EC2 Instance Types](#ec2-Instance-Types)
- [EC2 Security Groups](#EC2-Security-Groups)
- [EC2 SSH and Instance Connect](#EC2-SSH-and-Instance-Connect)
- [EC2 Instance Roles](#EC2-Instance-Roles)
- [EC2 Instances Purchasing Options](#EC2-Instances-Purchasing-Options)
- [EC2 Shared Responsibility Model](#EC2-Shared-Responsibility-Model)
- [EC2 Instance Storage](#EC2-Instance-Storage)
- [EC2 Elastic Load Balancing and Auto Scaling Groups](#EC2-Elastic-Load-Balancing-and-Auto-Scaling-Groups)

EC2 stands to Elastic Compute Cloud and is a service where you can rent virtual servers or machines called EC2 instances from AWS. This Cloud Computing is from Infrastructure as a Service (IaaS) type. EC2 is also a Region Scoped Service.

### EC2 Sizing & Configuration Options

- Operating System (OS): Linux,\Nindows or Mac OS
- How much compute power & cores (CPU)
- How much random-access memory (RAM)
- How much storage space:
  - Network-attached (EBS & EFS)
  - hardware (EC2 Instance Store)
- Network card: speed of the card, Public IP address
- Firewall rules: security group
- Bootstrap script (configure at first launch): EC2 User Data

### EC2 User Data

- EC2 User Data script is used for bootstrapping.
- Bootstrapping means launching a piece of code at the time when a machine starts.
- This EC2 User Data Script runs once when the instane first start.
- This script runs with the root user.
- This script is used to automate boot tasks such as:
  - Installing updates, softwares.
  - Downloading common files from the internet.
  - And much more!


### How to Create a Basic EC2 Instance

To create an EC2 Instance we need to follow the bellow steps on EC2 Console:

- Select an AMI (Amazon Machine Image). It is the operating system. (most common is Amazon Linux 2 and it is free tier)
- Select the Instance Type. It is the server hardware configurations (t2-micro is free tier)
- Configure Instance Details if you want to change anything. In this case, we can create the `user data` to instance, that is the code start to it. Bellow a script to print hello world on our instance.

```bash
#!/bin/bash
# Use this for your user data (script from top to bottom)
# install httpd (Linux 2 version)
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
echo "<h1>Hello World from $(hostname -f)</h1>" > /var/www/html/index.html
```

- Create the storage: It is data disk available in our instance and it is customizable. By default Whenever you launch an EC2 instance, the instance store volume type is ephemeral (Amazon EC2 instance store)

- Create a Security Group (SG) and add a rule to HTTP allows access from anywhere, `Port 80 and Source 0.0.0.0/0, ::/0`

- Finally Review and Launch. (Save the Key/Par Generated). A few seconds later the instance will be available and accessible.

- When finish it, just stop/terminate the instance.

---

### Amazon Machine Image

**AMIs** stands to Amazon Machine Image and are a customization of an EC2 Instance:

- You must use an AMI from the same region as that of the EC2 instance. (The region of the AMI has no bearing on the performance of the EC2 instance)
- We can add our own software configurations, operating systems, monitoring and others configurations.
- This will leverage to a faster boot, because the software is pre-packed.
- AMIs are built for a specific region (can be copied across them)
- We can launch AMIs: 1) provided by AWS, 2) our own AMI or 3) AMIs from AWS Marketplace.
- We can create an AMI starting from an instance. It will create the AMI and a snapshot of the storage.

**EC2 Image Builder**:

- Used to automate the creation of Virtual Machine or Container Images.
- Automatically: Create, maintain, validate and test AMIs
- The builder can run scheduled (when a pack is updated, weekly, etc...)
- It is a free service (pay for the resources)

In order to create it by Image Builder we need to create an `Image Pipeline`, `Recipe`, `Define infrastructure` and `Define Distribution`:

- This pipeline will have a name/description and a schedule (By time, by Cron or Manual)
- After that we must choose or create a `Recipe`: In this Recipe we choose if it is a AMI or a Docker image, Select the OS, the components to be installed with the image (such as Java, AWS Cli), and the tests that we can do to check if the image has the correct components.
- After, we must select `Define infrastructure`: To do it we need to create a Role for the Image Builder execute actions in our behalf, it is an EC2 Role. Then we select the instance Type (e.g. t2.micro) and other basic configurations.
- Finally, we `Define Distribution`, by choosing the Region(s) to be available and ready to use.

Since this pipeline is ready we can execute anytime to build our AMI. This pipeline will create an EC2 instance (where the AMI will be created based on) and terminate it. As well an instance will be created to test and after testing phase, it will be terminated. Finally it will create the distribution and the AMI will be available to use.

<p align="center" width="100%"><img src="assets/image-builder.jpg" alt="image-builder" width="500"/></p>

---

### EC2 Instance Types

**AWS has the following naming convention:**
m5.2xlarge

- m: instance class
- 5: generation of the instance (AWS improve them over time)
- 2xlarge: the size of the instance. (Memory, CPU, etc... )

There are a few [instance types]([https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html](https://aws.amazon.com/ec2/instance-types/)):

**General Purpose**:

- Great for a diversity of workloads such as web services and code repositories.
- Use cases: Small and midsize databases, Data processing tasks that require additional memory, Caching fleets.
- This one have a good balance between the compute, memory and networking.
- Instance Class: T and M. e.g. t2.micro
  
**Compute Optimized**:

- Great for compute-intensive workloads that require high performance processors.
- Services such as Batch processing, media transcode, High performance web servers, High performance compute (HPC), Scientific modeling and Machine Learning or Dedicated Gaming Servers.
- For now, all the Compute Optimized Instance have naming starting with _C_, like c5.large.

**Memory Optimized**:

- These types have fast performance for workloads that require in-memory processing ( requires more RAM).
- Use cases: High-performance, relational (MySQL) and NoSQL (MongoDB, Cassandra) databases. Distributed web scale cache stores that provide in-memory caching of key-value type data (Memcached and Redis). In-memory databases using optimized data storage formats and analytics for business intelligence (for example, SAP HANA). Applications performing real-time processing of big unstructured data (financial services, Hadoop/Spark clusters). High-performance computing (HPC) and Electronic Design Automation (EDA) applications.
- Most of the instances of this type starts with _R_ (R stnads for RAM). We also have instances starting with _X_ and _U_.

**Storage Optimized**

- Made for storage-intense tasks that require high, sequential Read and Write access to large data sets on local storage. 
- Use cases: High Frequency Online Transaction Processing (OLTP) systems, Relational and NoSQL databases, Cache for in-memory database (like Redis), Data warehousing, Distributed file systems.
- Most of the instances of this type starts with _D_ , _H_ and _I_.

**Accelerated Computing**:

- Similar to Memory optimized, but this one use hardware accelerators, or co-processors, to perform functions such as floating point number calculations, graphics processing, or data pattern matching, more efficiently than is possible in software running on CPUs.
- Most of the instances of this type starts with _DL_ , _F_ , _G_ , _P_ and _Trn_.

**High-Performance Computing(HPC)**

- High performance computing (HPC) instances are purpose built to offer the best price performance for running HPC workloads at scale on AWS.
- Most of the instances of this type starts with _Hpc_.

---

### EC2 Security Groups

- They control how traffic is allowed into our EC2 instances. It is the Firewall of the instance.
- They contain only **allow** rules
- Reference by IP or by other Security Group
- Regulate the Access to the Ports, Authorized IP ranges (IPV4 and IPv6)
- Control of inbound network (from outside to inside the instance) - by default all inbound traffic is blocked. 
- Control of outbound network (from instance to outside) - by default, all traffic outbound (from our instance to the rest of the world) is allowed. 
- Common Ports that we need to know

<p align="center" width="100%"><img src="assets/security-group.jpg" alt="security-group" width="400"/></p>

**Good to Know about Security Groups**

- One SG can be attached to multiple instances.
- Each intance can have multiple SGs.
- SG are specific to region/VPC combination. If you chage the region, you have to create a new SG.
- Security Group is not an application that runs on EC2. It's a firewall that lives outside the EC2.
- It's good to maintain one separate security group for SSH access.
- **If your application is not accessible (time out), then it's a security group issue**

### Classic Ports to know

```
22 - SSH (Secure Shell) - log into EC2 Linux instance
22 - SFTP - (Secure File Transport Protocol) - upload file using SSH
21 - FTP (File Transfer Protocol) - upload files into a file share
80 - HTTP - access unsecured websites
443 - HTTPS - access secured websites
3389 - RDP (Remote Desktop Protocol) - Log into a windows instance
```

---

### EC2 SSH and Instance Connect

- SSH is a CLI tool that allows you to control a remote machine, update, and lots of configurations, all using the command line.
- To access Mac, Linux and Win 10+ use SSH and to access Windows <10 use Putty!

```bash
# check permissions on .pem file (must be chmod 0400)
# and then log using ssh:

ssh -i permissionsFile.pem ec2-user@ec2-public-ip
```

- **EC2 Instance Connect** is an Second Option to run SSH directly from Console. It runs a browser based SSH instance connection/terminal.
- In this case a temporary Key is uploaded to EC2 instance by AWS. Works only out-of-the-box with Amazon Linux 2.
- Remember to Allow port 22 in Security Group.

---

**SSH Troubleshooting**

- **There's a connection timeout** - This is a security group issue. Any timeout (not just for SSH) is related to security groups or a firewall. Ensure your security group looks like this and correctly assigned to your EC2 instance.
![image](https://github.com/user-attachments/assets/0dd8e40b-0dcf-496f-8c2d-86b97b9e483b)
- **There's still a connection timeout issue** - If your security group is properly configured as above, and you still have connection timeout issues, then that means a firewall(corporate or a personal) is blocking the connection.
-  **SSH does not work on Windows** - It means you have to use Putty.
-  **There's a connection refused** - This means the instance is reachable, but no SSH utility is running on the instance.  
  -  Try to restart the instance and If it doesn't work, terminate the instance and create a new one.
  -  Make sure you're using Amazon Linux 2.
- **Permission denied (publickey,gssapi-keyex,gssapi-with-mic)** - This means either two things:
  - You are using the wrong security key or not using a security key. Please look at your EC2 instance configuration to make sure you have assigned the correct key to it.
  - You are using the wrong user. Make sure you have started an Amazon Linux 2 EC2 instance, and make sure you're using the user ec2-user. This is something you specify when doing ec2-user@<public-ip> (ex: ec2-user@35.180.242.162) in your SSH command or your Putty configuration.
- **Nothing is working - "aaaahhhhhh"** - Don't panic. Use EC2 Instance Connect from the next lecture. Make sure you started an Amazon Linux 2 and you will be able to follow along with the tutorial :)
-  **I was able to connect yesterday, but today I can't** - This is probably because you have stopped your EC2 instance and then started it again today. When you do so, the public IP of your EC2 instance will change. Therefore, in your command, or Putty configuration, please make sure to edit and save the new public IP.

---

### EC2 Instance Roles

- In order to run or access aws services, usually in our terminal we would use `aws configure`and pass our credentials.
- But in EC2 instance it is not a good practice, since it is a server and can be managed by more than one person. So a person can retieve our details. **Never ever put your IAM Access keys and secrets into the EC2 instance.**
- So if you want to EC2 to perform an action in other services in our behalf, we need to create a role to this EC2.

- For example: if we want to execute the command `aws iam list-users` we need to create a role to that contains the IAM Policy, in this case, the read user permission. To do it we need to create the policy and the role in our IAM Console and after that, in EC2 console, attach the role to the EC2 instance. Now our instance can perform this list-users action without require your secret keys directly inside the server.

---

### EC2 Instances Purchasing Options

EC2 Instances in the cloud, the default type of EC2 is on-demand instances.

**On-demand instances**

- No commitment to user to specific time.
- Full control over the life-cycle of instance: when start, stop, etc.
- Pay only for the seconds that the instance is on state Running.
- Recommend to short workloads and irregular runnings that cannot be stopped until finish.

**Reserved Instances**:

- Minimum commitment of one year, maximum of three years.
- Up to 72% discount compared to On-Demand.
- Purchasing options: no upfront | partial upfront | all upfront (upfront = pay all now and get more discount).
- Can buy a instance that runs in one specific Region [Regional] or Specific Zone [Zonal] - it affects the price.
- There are three kinds of reserved instances:

1. **Reserved Instances**: for long workloads and always use the selected type of instance during the commitment time. (use case: host a database)
2. **Convertible Reserved Instances**: long workloads with flexible instances (can change the type of the instance over time)
3. **Scheduled Reserved Instances**: When you need to use the instance only in scheduled time. For example: when you need to use the instance every monday from 6am to 11am for one year.

About the prices: A Standard Reserved Instance provides a more significant discount than a Convertible Reserved Instance.

| Action                                       | Standard Reserved Instance | Convertible Reserved Instance                                                                  |
| -------------------------------------------- | -------------------------- | ---------------------------------------------------------------------------------------------- |
| Modify Instance                              | Modify Attributes          | Modify Attributes                                                                              |
| Exchanging Reserved Instances                | Can't be exchanged.        | Can be exchanged during the term for another Convertible Reserved Instance with new attributes |
| Selling in the Reserved Instance Marketplace | Can be sold.               | Can't be sold.                                                                                 |
| Buying in the Reserved Instance Marketplace  | Can be bought.             | Can't be bought.                                                                               |

**Spot Instances**: Amazon EC2 Spot Instances let you take advantage of unused EC2 capacity in the AWS cloud. Spot Instances are available at up to a 90% discount compared to On-Demand prices.

- You can "lose" at any time, because spot price changes.
- Can be combined with On-demand Instances
- Up to 90% of discount compared to On-Demand
- Has access to multiple AWS services.
- You can use Spot Instances for various stateless, fault-tolerant, or flexible applications such as big data, containerized workloads, CI/CD, web servers, high-performance computing (HPC), and test & development workloads.

**Dedicated Hosts**: Book an entire physical server.

- An Amazon EC2 Dedicated Host is a physical server with EC2 instance capacity fully dedicated to your use.
- This type address compliance requirements, reduce cost by allowing you to use your own licenses
- They allow you to use your existing per-socket, per-core, or per-VM software licenses means Dedicated Hosts allow you to bring and use your own software licenses that are often restricted by physical hardware metrics, such as:
  - Per-socket: Some software licenses are tied to the number of CPU sockets on a physical server. A socket is where a physical CPU chip is installed. Most motherboards have one, two, or four sockets.
  - Per-core: Some licenses are tied to the number of CPU cores on the physical server. Each CPU chip may have multiple cores.
  - Per-VM (Virtual Machine): Some licenses are based on the number of virtual machines (VMs) running on a host.
- Three year reservation period
- More expensive
- Useful to compliance needs/regulatory issues, use software that require licenses.

**EC2 Dedicated Instances**: Hardware dedicated to the customer.

- Can share the hardware to othrer instances if instances are within the same account.
- It is a soft version of dedicated hosts.

Difference between Dedicated Hosts and EC2 Dedicated Instances

- Both allow you to use dedicated server, but in the EC2 Dedicated Hosts you pay for each Host and can have more access to the hardware it is much more flexible and is recommended when you have server bound licenses, while in EC2 Dedicated Instances you pay for each instance and cannot have access to hardware.


**EC2 Capacity Reservations**

- Reserve On-Demand instances capacity in a specific AZ for any duration
You always have access to EC2 capacity when you need it.
- No time commitment (create/cancel anytime), no billing discounts.
- Combine with Regional Reserved Instances and Savings Plans to benefit
from billing discounts.
- You're charged at On-Demand rate whether you run instances or not
- Suitable for short-term, uninterrupted workloads that needs to be in a
specific AZ

---

### EC2 Shared Responsibility Model

**AWS Responsibility**

- Infrastructure (global network security)
- Isolation of physical hosts
- Replace Faulty Hardware
- Compliance validation

**User Responsibility**

- Security Groups rules
- Operating System patches and updates
- Software installed inside the instance
- IAM Roles to/on EC2 and users access
- Data security on your instance

**Shared Responsibility**:

> Security and Compliance is a shared responsibility between AWS and the customer. This shared model can help relieve the customer’s operational burden as AWS operates, manages and controls the components from the host operating system and virtualization layer down to the physical security of the facilities in which the service operates.
