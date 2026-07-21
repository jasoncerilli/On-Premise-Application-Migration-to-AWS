# On-Premise Application Migration to AWS (EC2 + RDS + DMS)

![AWS](https://img.shields.io/badge/AWS-Cloud-orange?logo=amazonaws&logoColor=white)
![Migration](https://img.shields.io/badge/Migration-Lift%20%26%20Shift-blue?logo=amazonaws)
![DMS](https://img.shields.io/badge/Database-Migration%20Service-yellow?logo=amazonaws)
![RDS](https://img.shields.io/badge/Database-RDS%20MySQL-orange?logo=amazonaws)
![Status](https://img.shields.io/badge/Status-Deployed%20%26%20Validated-brightgreen)

> **Read the full project write-up on Medium:** [link]
> **Connect on LinkedIn:** [link]

---

## TL;DR

A lift-and-shift migration of a two-tier application from a simulated on-premise environment to AWS. The application layer moves to EC2 and the MySQL database migrates to Amazon RDS using AWS Database Migration Service (DMS) — the same tooling used by enterprises migrating production workloads to the cloud.

---

## What This Project Demonstrates

- Planning and executing a lift-and-shift cloud migration
- Setting up AWS DMS with source and target endpoints, replication instance, and migration tasks
- Migrating a MySQL database to Amazon RDS with minimal downtime
- Configuring VPC networking to support migration connectivity
- Deploying and configuring EC2 as the migrated application host
- Verifying data integrity post-migration

---

## Prerequisites

- AWS account with admin IAM user
- Basic understanding of relational databases and MySQL
- SSH client for EC2 access

---
## Architecture Overview

![On-prem Architecture Diagram](photos/onprem.png)

## AWS Services Used

| Service | Purpose |
|---|---|
| Amazon EC2 | Hosts the migrated application layer |
| Amazon RDS MySQL | Target database — managed, scalable, automated backups |
| AWS Database Migration Service | Migrates data from source MySQL to target RDS |
| Amazon VPC | Network isolation and connectivity between components |
| AWS IAM | Access control for DMS and EC2 roles |

---

## DMS Configuration

### Replication Instance

| Setting | Value |
|---|---|
| Instance Class | `dms.t3.micro` |
| Engine Version | Latest stable |
| Multi-AZ | No (single AZ for this project) |
| Storage | 50 GB |
| VPC | Project VPC |

### Source Endpoint (MySQL on EC2)

| Setting | Value |
|---|---|
| Engine | MySQL |
| Server Name | EC2 private IP |
| Port | 3306 |
| Username | Migration user |
| SSL Mode | None (internal VPC traffic) |

### Target Endpoint (Amazon RDS MySQL)

| Setting | Value |
|---|---|
| Engine | Amazon Aurora MySQL / RDS MySQL |
| Server Name | RDS endpoint URL |
| Port | 3306 |
| Username | RDS admin user |

### Migration Task

| Setting | Value |
|---|---|
| Migration Type | Full load |
| Table mappings | All tables from source schema |
| LOB settings | Limited LOB mode |

> [📸 Screenshot: DMS replication instance in Available state]
> [📸 Screenshot: Source and target endpoints showing Successful connection test]
> [📸 Screenshot: Migration task showing Load Complete status]

---

## Implementation Breakdown

### Implementation 1: Set Up the Source Environment

Provisions the simulated on-premise environment — an EC2 instance running MySQL with a pre-populated database. This becomes the migration source.

**Validated:** MySQL running on EC2, database populated with test records, accessible on port 3306 from within the VPC.

> [📸 Screenshot: EC2 instance running with MySQL service active]
> ![On Prem Server](photos/laucnhinstance.png)

---

### Implementation 2: Provision the Target RDS Instance

Creates an RDS MySQL instance in a private subnet as the migration target. Configures the subnet group, security group, and parameter group. Backup retention and encryption enabled.

**Validated:** RDS instance in Available state, connectivity confirmed from EC2.

> [📸 Screenshot: RDS console showing instance in Available state]
> [📸 Screenshot: Successful MySQL connection from EC2 to RDS endpoint]

---

### Implementation 3: Configure and Run AWS DMS

Creates the DMS replication instance, configures source and target endpoints with connection tests, then creates and runs a full-load migration task.

**Validated:** Migration task completes with Load Complete status. Table counts match between source and target. Data integrity confirmed by querying both databases.

> [📸 Screenshot: DMS migration task showing 100% table load complete]
> [📸 Screenshot: Table statistics showing matching row counts source vs target]

---

### Implementation 4: Validate and Cut Over

Queries both the source MySQL and target RDS to confirm data integrity. Updates the application configuration to point to the RDS endpoint. Final validation confirms the application is running fully on AWS.

> [📸 Screenshot: Query results matching between source and target databases]
> [📸 Screenshot: Application running on EC2 connecting to RDS endpoint]

---

## Key Design Decisions

**Why DMS instead of a manual mysqldump?**
DMS is the production-standard tool for database migrations on AWS. It handles data type mapping, large datasets, connection management, and provides monitoring and logging out of the box. Using DMS demonstrates the skill that actually matters in enterprise migration projects.

**Why RDS instead of MySQL on EC2?**
RDS removes the operational overhead of managing a database server — automated backups, patching, failover, and monitoring are all handled by AWS. For a migration project, this is the correct target: lift the application layer (EC2) and modernise the database layer (RDS) simultaneously.

**Why a private subnet for RDS?**
The database should never be directly reachable from the internet. Placing RDS in a private subnet and restricting inbound port 3306 to only the EC2 security group enforces this correctly.

---

## Skills Demonstrated

- Cloud migration planning and execution (lift-and-shift)
- AWS Database Migration Service (DMS) configuration
- RDS provisioning and database connectivity
- VPC networking for migration architecture
- Data integrity validation post-migration
- Application cutover to cloud infrastructure

---

## Related

- Medium article: [link]
