<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# Aurora Database with EC2

**Project Link:** [View Project](https://nextwork.ai/projects/cee72c3a-f285-55a6-8217-3cee4023a3bf)

**Author:** Maria Jasmin Ahorro  
**Email:** jasminahorro23@gmail.com

---

![Image](https://nextwork.ai/overjoyed_magenta_proud_yak/uploads/cee72c3a-f285-55a6-8217-3cee4023a3bf_44443546)

## Introducing Today's Project!

### What is Amazon Aurora?

Amazon Aurora is a fully managed, high-performance relational database service provided by AWS that is compatible with MySQL and PostgreSQL, and it is useful because it offers high availability, automated scaling, and fault-tolerant storage across multiple Availability Zones at a fraction of the cost of traditional commercial databases.

### How I used Amazon Aurora in this project

In today's project, I used Amazon Aurora to create a relational MySQL-compatible database cluster and connect it securely to an Amazon EC2 web server, setting up a scalable backend to store and serve data for a web application.

### One thing I didn't expect in this project was...

One thing I didn't expect in this project was how seamlessly AWS automatically configured the security group and network connection between my EC2 web server and the Aurora database cluster when I selected the "Connect to an EC2 compute resource" option.

### This project took me...

It took me about 1 hour to complete this project.

## In the first part of my project...

### Creating an Aurora Cluster

A relational database is a type of database that stores and organizes data into tables with predefined rows and columns. It allows users to query and manipulate data using SQL (Structured Query Language) and establishes relationships between different tables.

Aurora is a good choice when you need a high-performance, enterprise-grade relational database that offers automatic scaling, high availability across multiple availability zones, and seamless MySQL or PostgreSQL compatibility for heavy, mission-critical workloads.

![Image](https://nextwork.ai/overjoyed_magenta_proud_yak/uploads/cee72c3a-f285-55a6-8217-3cee4023a3bf_44443546)

## Halfway through I stopped!

I stopped creating my Aurora database because I needed to select an EC2 instance to connect to, but I haven't created the EC2 instance yet.

### Features of my EC2 instance

I created a new key pair for my EC2 instance because key pairs act as secure digital credentials required to authenticate and connect to the virtual machine using SSH, ensuring that only authorized users can access the server.

When I created my EC2 instance, I took particular note of the Public IPv4 DNS (or Public IPv4 address) and the Key pair name, because the DNS provides the exact location needed to access the instance and the key pair serves as the required security credentials to authenticate and log in.

![Image](https://nextwork.ai/overjoyed_magenta_proud_yak/uploads/cee72c3a-f285-55a6-8217-3cee4023a3bf_91b9fd1g)

## Then I could finish setting up my database

![Image](https://nextwork.ai/overjoyed_magenta_proud_yak/uploads/cee72c3a-f285-55a6-8217-3cee4023a3bf_1fddb0b5)

Aurora Database uses clusters because separating database compute and storage into a cluster architecture provides high availability, fault tolerance, and scalable performance. By storing multiple copies of your data across different Availability Zones, an Aurora cluster ensures that if a database node fails, another can automatically take over with minimal downtime and no data loss. Furthermore, this cluster design enables you to run a primary Writer instance alongside multiple Reader instances to effortlessly handle higher traffic, while the underlying distributed storage automatically expands as your data grows independently of compute capacity.

## Notes

- **Cost:** Aurora is not part of the AWS Free Tier. After the project I deleted the Aurora cluster (and its writer/reader instances), the EC2 instance, the key pair, and the security groups AWS created for the EC2 ↔ Aurora connection (`ec2-rds-*` / `rds-ec2-*`).
- **Key pairs:** the `.pem` file is a private key. It stays on my computer and is never committed to this repo.
- See [CHEATSHEET.md](CHEATSHEET.md) for lesson notes and a self-quiz.

---

*Built with [NextWork](https://nextwork.ai) - [View this project](https://nextwork.ai/projects/cee72c3a-f285-55a6-8217-3cee4023a3bf)*
