# Aurora Database with EC2 — Cheat Sheet

> 📚 **Part 1 of 2** in my Aurora projects · Next: [Connect a Web App with Aurora](https://github.com/jasahorro/Connect-a-Web-App-with-Aurora/blob/main/CHEATSHEET.md) →

Lesson notes for reviewing later. The project write-up is in [README.md](README.md).

## Contents

1. [What I built](#what-i-built)
2. [Key terms](#key-terms)
3. [Relational databases and Aurora](#relational-databases-and-aurora)
4. [Instance configuration (DB instance classes)](#instance-configuration-db-instance-classes)
5. [Aurora clusters: writer and readers](#aurora-clusters-writer-and-readers)
6. [Connecting EC2 to Aurora](#connecting-ec2-to-aurora)
7. [Build order and cleanup](#build-order-and-cleanup)
8. [Self-quiz](#self-quiz)

---

## What I built

- 🧱 **An Aurora MySQL database** created from scratch, configured, and connected to an EC2 instance so it's ready to back a web app.
- 💻 **An EC2 instance** running the **Amazon Linux 2023** AMI, with a new key pair and the security groups needed to let it talk to the database.

```
  You (SSH with .pem key)
          │
          ▼
  ┌───────────────┐  port 3306 (MySQL)  ┌──────────────────────┐
  │ EC2 web server│ ──────────────────► │ Aurora MySQL cluster │
  │ Amazon Linux  │                     │ writer (+ readers)   │
  └───────────────┘                     └──────────────────────┘
     sg: ec2-rds-*                         sg: rds-ec2-*
```

## Key terms

| Term | Plain meaning | Analogy |
|---|---|---|
| **Relational database** | Data stored in tables (rows and columns) that can link to each other; queried with SQL | A set of spreadsheets whose sheets reference each other |
| **SQL** | The language for reading and changing relational data | The questions you're allowed to ask the librarian |
| **Amazon Aurora** | AWS's fully managed relational database, compatible with MySQL and PostgreSQL | A restaurant kitchen AWS runs for you; you just send orders |
| **Cluster** | The group of DB instances plus the shared storage they all use | One shared filing cabinet with several clerks working from it |
| **DB instance** | The virtual computer a database runs on | The clerk doing the work |
| **Instance class** | How much CPU and memory that computer has | Picking which size of engine goes in the car |
| **EC2 instance** | A virtual server in AWS (here, the web server) | A rented computer in AWS's data centre |
| **AMI** | The template the EC2 instance boots from (here, Amazon Linux 2023) | The operating system installer disc |
| **Key pair** | Public/private keys for logging in to EC2 over SSH; you keep the `.pem` | A house key that only you hold a copy of |
| **Security group** | A firewall around a resource that says which traffic is allowed in or out | A bouncer with a guest list |

## Relational databases and Aurora

- A **relational database** organises data into **tables** with set **rows and columns**, and lets tables relate to each other (for example, an `orders` table that points to a `customers` table).
- **Aurora** is a good fit when you need high performance, **high availability across multiple Availability Zones**, automatic storage growth, and MySQL/PostgreSQL compatibility for important workloads.
- "Fully managed" means AWS handles the hardware, patching, backups and replication. You handle the schema and the data.

## Instance configuration (DB instance classes)

**Instance configuration** is where you pick the virtual computer your Aurora database runs on. Just like Word needs your laptop to run, the database needs a computer to run on too.

- A bigger instance class gives more CPU and memory, so the database performs better, but it **costs more**.

| Class | Best for | Example | Analogy |
|---|---|---|---|
| **Burstable** (`db.t*`) — *used in this project* | A steady, low baseline with occasional random spikes | A small app that's quiet most of the day and busy at lunchtime | A car that cruises cheaply and has a turbo button for short bursts |
| **Serverless v2** | Unpredictable or new workloads; you pay for the capacity you use | A brand-new app with no idea yet what traffic looks like | A taxi meter: you pay for the distance you actually travel |
| **Memory optimized** (`db.r*`) | Heavy, memory-intensive work: big data sets and complex queries | A large e-commerce site filtering and searching thousands of products | A truck: expensive to run, but it can carry a lot |

> **Note:** burstable instances earn CPU "credits" while they're quiet and spend them during bursts. If the database is busy *all the time*, the burst never ends. On Aurora that means extra charges or slower performance, so a constantly busy database belongs on a bigger class.

## Aurora clusters: writer and readers

- Aurora **separates compute (instances) from storage**. The storage is shared by the whole cluster.
- The data is automatically copied across **multiple Availability Zones** (6 copies across 3 AZs), so a single failure doesn't lose data.
- **Writer instance** (the primary): handles all writes, and reads too.
- **Reader instances** (replicas): handle extra read traffic. If the writer fails, a reader can be promoted to take over (**failover**) with very little downtime.
- **Storage grows automatically** as your data grows, independently of the instance size.

| Part | Job | Analogy |
|---|---|---|
| Shared storage | Holds the data, copied across AZs | The filing cabinet, with photocopies kept in 3 buildings |
| Writer | Changes the data | The one clerk allowed to write in the files |
| Readers | Answer read requests; standby for failover | Clerks who can only look things up, and step in if the writer goes home sick |

## Connecting EC2 to Aurora

- In the Aurora setup there's a **"Connect to an EC2 compute resource"** option. Picking your instance makes AWS set up the network for you:
  - It creates two security groups: **`ec2-rds-*`** (attached to EC2) and **`rds-ec2-*`** (attached to the database).
  - The database's group only allows MySQL traffic (**port 3306**) coming **from the EC2's group**. So only your web server can reach the database, not the whole internet.
- That's why the build had to **pause halfway**: the EC2 instance had to exist before Aurora could be connected to it.
- Things to note down when launching EC2:
  - **Public IPv4 DNS / address**: where to connect to the server.
  - **Key pair name** (and the `.pem` file): the credentials for logging in.

## Build order and cleanup

1. Start creating the Aurora cluster (engine: **Aurora MySQL**; instance class: **Burstable**).
2. **Stop** when it asks for an EC2 instance to connect to.
3. Launch an EC2 instance (**Amazon Linux 2023**) with a **new key pair**. Note its DNS and key pair name.
4. Go back and finish the Aurora setup, picking **Connect to an EC2 compute resource** → your instance.
5. Wait for the cluster's status to show **Available** (it takes a few minutes).

**Cleanup (Aurora isn't in the Free Tier):** delete the DB instances and cluster → terminate the EC2 instance → delete the key pair → delete the `ec2-rds-*` and `rds-ec2-*` security groups.

---

## Self-quiz

<details><summary>1. What is a relational database, and what language do you use to query it?</summary>

A database that stores data in tables of rows and columns that can relate to each other. You query it with **SQL**.
</details>

<details><summary>2. Which two database engines is Aurora compatible with?</summary>

**MySQL** and **PostgreSQL**.
</details>

<details><summary>3. What does "instance configuration" decide?</summary>

The type of virtual computer the database runs on, meaning how much CPU and memory it has. That sets both its performance and its price.
</details>

<details><summary>4. When should you pick a Burstable class?</summary>

When traffic is a steady, low baseline with occasional random spikes. It's cheap when quiet but can still handle a burst.
</details>

<details><summary>5. A brand-new app has no idea what its traffic will look like. Which class fits best?</summary>

**Serverless v2**. It scales automatically with usage, so you pay for what you use.
</details>

<details><summary>6. A large shop runs complex product filters over a huge catalogue. Which class fits best?</summary>

**Memory optimized**. It's built for heavy, memory-intensive queries.
</details>

<details><summary>7. Why does Aurora use clusters?</summary>

Separating compute from shared, multi-AZ storage gives high availability and fault tolerance (a reader can take over if the writer fails). It also lets reads scale out across readers, and storage grows automatically.
</details>

<details><summary>8. What's the difference between the writer and a reader instance?</summary>

The writer handles writes (and reads). Readers only handle reads, and one can be promoted to writer during a failover.
</details>

<details><summary>9. Why did you stop halfway through creating the database?</summary>

The setup wanted an EC2 instance to connect to, and none existed yet.
</details>

<details><summary>10. What did "Connect to an EC2 compute resource" set up for you?</summary>

Two security groups (`ec2-rds-*` on EC2 and `rds-ec2-*` on the database) that allow MySQL traffic on port 3306 only from the EC2 instance to the database.
</details>

<details><summary>11. Which two details should you note when launching the EC2 instance, and why?</summary>

The **Public IPv4 DNS/address** (where to connect) and the **key pair name** (the credentials to log in with).
</details>

<details><summary>12. Why must a `.pem` file never be pushed to GitHub?</summary>

It's the private key. Anyone who has it can log in to your EC2 instance.
</details>
