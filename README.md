


<br/>

<!-- BADGES ROW 1 -->
![AWS](https://img.shields.io/badge/Amazon_AWS-FF9900?style=for-the-badge&logo=amazonaws&logoColor=white)
![Architecture](https://img.shields.io/badge/Architecture-Three--Tier-blue?style=for-the-badge&logo=awslambda&logoColor=white)
![Status](https://img.shields.io/badge/Status-Production_Ready-brightgreen?style=for-the-badge&logo=checkmarx&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-yellow?style=for-the-badge)

<!-- BADGES ROW 2 -->
![VPC](https://img.shields.io/badge/VPC-10.0.0.0%2F16-orange?style=flat-square&logo=amazonaws)
![AZs](https://img.shields.io/badge/Availability_Zones-3-red?style=flat-square&logo=amazonaws)
![RDS](https://img.shields.io/badge/RDS-Multi--AZ-blue?style=flat-square&logo=amazondynamodb)
![ALB](https://img.shields.io/badge/Load_Balancers-2-purple?style=flat-square&logo=amazonaws)
![EC2](https://img.shields.io/badge/EC2-Auto_Scaling-green?style=flat-square&logo=amazonec2)
![Security](https://img.shields.io/badge/Security-Least_Privilege-critical?style=flat-square&logo=awssecretsmanager)

<br/>

> **🚀 A production-grade, cloud-native Three-Tier Architecture deployed on AWS — showcasing real-world infrastructure design with Multi-AZ High Availability, layered security, and auto-scaling compute.**

<br/>

---

</div>

## 📖 Table of Contents

| # | Section |
|---|---------|
| 1 | [🎯 Project Overview](#-project-overview) |
| 2 | [🏛️ Architecture Overview](#%EF%B8%8F-architecture-overview) |
| 3 | [🧱 Tier Breakdown](#-tier-breakdown) |
| 4 | [📐 Phase-by-Phase Implementation](#-phase-by-phase-implementation) |
| 5 | [🔐 Security Architecture](#-security-architecture) |
| 6 | [⚒️ Components Reference Table](#%EF%B8%8F-components-reference-table) |
| 7 | [🎓 Learning Outcomes](#-learning-outcomes) |
| 8 | [📁 Folder Structure](#-folder-structure) |
| 9 | [👤 Contact](#-contact) |

---

## 🎯 Project Overview

<div align="center">

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│   INTERNET  ──►  Frontend ALB  ──►  Web Tier (EC2)         │
│                                         │                   │
│                                         ▼                   │
│                              Backend ALB  ──►  App Tier     │
│                                                    │         │
│                                                    ▼         │
│                                          DB Tier (RDS)       │
│                                        [Multi-AZ Enabled]    │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

</div>

This project demonstrates how to design and deploy a **fully production-grade Three-Tier Architecture** on Amazon Web Services (AWS). The infrastructure is engineered for:

- ✅ **High Availability** — Resources span **3 Availability Zones** with failover built-in
- ✅ **Security** — Strict least-privilege Security Groups with **zero direct internet exposure** for App/DB tiers
- ✅ **Scalability** — Auto Scaling Groups dynamically manage EC2 capacity
- ✅ **Resilience** — Multi-AZ RDS ensures **zero-downtime** database failover
- ✅ **Network Isolation** — VPC with public/private subnet segmentation

---

## 🏛️ Architecture Overview

### 🌐 High-Level Traffic Flow

```mermaid
flowchart TB
    subgraph VPC["🏠 VPC (10.0.0.0/16)"]
        
        subgraph AZA["⚡ Availability Zone A"]
            PWA["🌐 public-web-subnet-a\n10.0.8.0/28"]
            PVA["🔒 private-web-subnet-a\n10.0.48.0/28"]
            PAA["⚙️ private-app-subnet-a\n10.96.0.0/28"]
            PDA["🗄️ private-db-subnet-a\n10.144.0.0/28"]
            FE_SRV_A["💻 Frontend Server A"]
            BE_SRV_A["⚙️ Backend Server A"]
            DB_A["🗄️ Database Node A"]
        end

        subgraph AZB["⚡ Availability Zone B"]
            PWB["🌐 public-web-subnet-b\n10.8.16.0/28"]
            PVB["🔒 private-web-subnet-b\n10.8.64.0/28"]
            PAB["⚙️ private-app-subnet-b\n10.112.0.0/28"]
            PDB["🗄️ private-db-subnet-b\n10.168.0.0/28"]
            FE_SRV_B["💻 Frontend Server B"]
            BE_SRV_B["⚙️ Backend Server B"]
            DB_B["🗄️ Database Node B"]
        end

        subgraph AZC["⚡ Availability Zone C"]
            PWC["🌐 public-web-subnet-c\n10.8.32.0/28"]
            PVC["🔒 private-web-subnet-c\n10.8.88.0/28"]
            PAC["⚙️ private-app-subnet-c\n10.128.0.0/28"]
            PDC["🗄️ private-db-subnet-c\n10.184.0.0/28"]
        end

        ALB_F["🔀 Frontend ALB"]
        ALB_B["🔀 Backend ALB"]
    end

    INET((🌍 Internet)) --> ALB_F
    ALB_F --> FE_SRV_A & FE_SRV_B
    FE_SRV_A & FE_SRV_B --> ALB_B
    ALB_B --> BE_SRV_A & BE_SRV_B
    BE_SRV_A --> DB_A
    BE_SRV_B --> DB_B
```

---

## 🧱 Tier Breakdown

<div align="center">

| Tier | Subnet Type | Components | CIDR Range |
|:----:|:-----------:|:----------:|:----------:|
| 🌐 **Web Tier** | Public | Frontend ALB, EC2 Web Servers | `10.0.8.0/28` per AZ |
| ⚙️ **App Tier** | Private | Backend ALB, EC2 App Servers | `10.96.0.0/28` per AZ |
| 🗄️ **DB Tier** | Private (Isolated) | RDS Multi-AZ | `10.144.0.0/28` per AZ |

</div>

### Subnet Topology

```
VPC: 10.0.0.0/16
│
├── 🌐 PUBLIC (Web Tier)
│   ├── public-web-subnet-a  →  10.0.8.0/28   [AZ-A]
│   ├── public-web-subnet-b  →  10.8.16.0/28  [AZ-B]
│   └── public-web-subnet-c  →  10.8.32.0/28  [AZ-C]
│
├── 🔒 PRIVATE (Web Hosts)
│   ├── private-web-subnet-a →  10.0.48.0/28  [AZ-A]
│   ├── private-web-subnet-b →  10.8.64.0/28  [AZ-B]
│   └── private-web-subnet-c →  10.8.88.0/28  [AZ-C]
│
├── ⚙️ PRIVATE (App Tier)
│   ├── private-app-subnet-a →  10.96.0.0/28  [AZ-A]
│   ├── private-app-subnet-b →  10.112.0.0/28 [AZ-B]
│   └── private-app-subnet-c →  10.128.0.0/28 [AZ-C]
│
└── 🗄️ PRIVATE (DB Tier — FULLY ISOLATED)
    ├── private-db-subnet-a  →  10.144.0.0/28 [AZ-A]
    ├── private-db-subnet-b  →  10.168.0.0/28 [AZ-B]
    └── private-db-subnet-c  →  10.184.0.0/28 [AZ-C]
```

---

## 📐 Phase-by-Phase Implementation

### 🔷 Phase 1 — VPC & Subnet Foundation

> *Establishes the foundational network environment with proper resource segregation for security and availability.*

```mermaid
flowchart LR
    VPC[🏠 VPC\n10.0.0.0/16] --> PUB1[🌐 Public Subnet 1]
    VPC --> PUB2[🌐 Public Subnet 2]
    VPC --> PRI1[🔒 Private Subnet - App]
    VPC --> PRI2[🔒 Private Subnet - App]
    VPC --> DB1[🗄️ Private Subnet - DB]
    VPC --> DB2[🗄️ Private Subnet - DB]

    PUB1 --> IGW[🌍 Internet Gateway]
    PUB2 --> IGW
    PRI1 --> NAT[🚪 NAT Gateway]
    PRI2 --> NAT
```

**Steps:**
1. **Create VPC** with CIDR `10.0.0.0/16`
2. **Create 12 Subnets** across 3 AZs:
   - 3× Public Web Subnets
   - 3× Private Web Subnets
   - 3× Private App Subnets
   - 3× Private DB Subnets

---

### 🔷 Phase 2 — Connectivity & Routing

> *Configures how traffic enters (IGW) and how private resources access the internet securely (NAT Gateway).*

```mermaid
flowchart TD
    IGW[🌍 Internet Gateway] --> RT_Public["📋 Public Route Table\n0.0.0.0/0 → IGW"]
    RT_Public --> PUB1[Public Subnet 1]
    RT_Public --> PUB2[Public Subnet 2]

    NAT[🚪 NAT Gateway] --> RT_Private["📋 Private Route Table\n0.0.0.0/0 → NAT"]
    RT_Private --> PRI1[Private App Subnet 1]
    RT_Private --> PRI2[Private App Subnet 2]

    RT_DB["📋 DB Route Table\n❌ No default route\n(Full Isolation)"] --> DB1[Private DB Subnet 1]
    RT_DB --> DB2[Private DB Subnet 2]
```

**Steps:**
3. **Create & attach Internet Gateway (IGW)** to the VPC
4. **Allocate Elastic IP** → Create **NAT Gateway** in `public-web-subnet-a`
5. **Configure 3 Route Tables:**
   - 🌐 Public RT → `0.0.0.0/0` → IGW
   - 🔒 Private App RT → `0.0.0.0/0` → NAT Gateway
   - 🗄️ Private DB RT → **No default route** (fully isolated)
6. **Associate** each Route Table to its respective subnets

---

### 🔷 Phase 3 — Security Groups & EC2 Auto Scaling

> *Defines network security boundaries using the principle of least privilege, with Auto Scaling for resilience.*

```mermaid
flowchart TB
    INTERNET((🌍 Internet)) --> FE_ALB_SG["🛡️ frontend_alb_sg\nAllow :80 from 0.0.0.0/0"]
    FE_ALB_SG --> FE_SRV_SG["🛡️ frontend_server_sg\nAllow :80 from FE ALB SG only"]
    FE_SRV_SG --> BE_ALB_SG["🛡️ backend_alb_sg\nAllow :80 from FE Server SG only"]
    BE_ALB_SG --> BE_SRV_SG["🛡️ backend_server_sg\nAllow :80 from BE ALB SG only"]
    BE_SRV_SG --> DB_SG["🛡️ database_sg\nAllow :3306 from Backend SG only"]

    FE_SRV_SG --> FE_ASG[📈 Frontend ASG]
    FE_ASG --> FE_LT[📄 Frontend Launch Template]

    BE_SRV_SG --> BE_ASG[📈 Backend ASG]
    BE_ASG --> BE_LT[📄 Backend Launch Template]

    DB_SG --> RDS[(🗄️ RDS Database)]
```

**Steps:**
7. **Create 5 Security Groups** (chained least-privilege rules):

   | Security Group | Allows | Source |
   |:---|:---|:---|
   | `frontend_alb_sg` | HTTP :80 | `0.0.0.0/0` (Internet) |
   | `frontend_server_sg` | HTTP :80 | `frontend_alb_sg` only |
   | `backend_alb_sg` | HTTP :80 | `frontend_server_sg` only |
   | `backend_server_sg` | HTTP :80 | `backend_alb_sg` only |
   | `database_sg` | MySQL :3306 | `backend_server_sg` only |

8. **Create Launch Templates** (with IAM Roles for CloudWatch/S3)
9. **Create Auto Scaling Groups** for Frontend and Backend servers

---

### 🔷 Phase 4 — Load Balancers & Multi-AZ Database

> *Connects tiers via ALBs and establishes the highly available database layer.*

```mermaid
flowchart TB
    INTERNET((🌍 Internet)) --> FE_ALB[🔀 Frontend ALB\nPublic Subnets]
    FE_ALB --> FE_TG[🎯 Frontend Target Group]
    FE_TG --> FE_ASG[📈 Frontend ASG]
    FE_ASG --> FE_EC2[💻 Frontend EC2 Instances]

    FE_EC2 --> BE_ALB[🔀 Backend ALB\nPrivate App Subnets]
    BE_ALB --> BE_TG[🎯 Backend Target Group]
    BE_TG --> BE_ASG[📈 Backend ASG]
    BE_ASG --> BE_EC2[⚙️ Backend EC2 Instances]

    BE_EC2 --> RDS[("🗄️ RDS Database\nMulti-AZ Enabled\n✅ Automatic Failover")]
```

**Steps:**
9. **Create Frontend ALB** → Public Subnets → Target: Frontend ASG
10. **Create Backend ALB** → Private App Subnets → Target: Backend ASG
11. **Create DB Subnet Group** using Private DB Subnets
12. **Launch RDS Multi-AZ** instance into the DB Subnet Group

---

### 🏁 Full Architecture — Final State

```mermaid
flowchart TB
    subgraph VPC[🏠 VPC — Multi-AZ High Availability]
        direction TB
        subgraph AZ1[⚡ Availability Zone 1]
            PUB1[🌐 Public Subnet 1]
            APP1[🔒 Private App Subnet 1]
            DB1[🗄️ Private DB Subnet 1]
        end
        subgraph AZ2[⚡ Availability Zone 2]
            PUB2[🌐 Public Subnet 2]
            APP2[🔒 Private App Subnet 2]
            DB2[🗄️ Private DB Subnet 2]
        end
    end

    INTERNET((🌍 Internet)) --> IGW[🌍 IGW]
    PUB1 & PUB2 --> IGW
    APP1 & APP2 --> NAT[🚪 NAT Gateway]

    IGW --> ALB[🔀 Frontend ALB]
    ALB --> FE_TG[🎯 Frontend TG]
    FE_TG --> FE_ASG[📈 Frontend ASG]
    FE_ASG --> FE_EC2[💻 Frontend EC2]

    FE_EC2 --> BE_ALB[🔀 Backend ALB]
    BE_ALB --> BE_TG[🎯 Backend TG]
    BE_TG --> BE_ASG[📈 Backend ASG]
    BE_ASG --> BE_EC2[⚙️ Backend EC2]

    BE_EC2 --> RDS[("🗄️ RDS Multi-AZ")]
    RDS --> DB1 & DB2
```

---

## 🔐 Security Architecture

<div align="center">

```
╔══════════════════════════════════════════════════════════╗
║              SECURITY CHAIN — ZERO TRUST                 ║
║                                                          ║
║  [Internet] → [FE ALB SG] → [FE Server SG]              ║
║                                  ↓                       ║
║             [BE ALB SG] ← ─ ─ ─ ┘                       ║
║                  ↓                                       ║
║            [BE Server SG] → [Database SG]               ║
║                                                          ║
║  ✅ No tier can bypass the previous layer               ║
║  ✅ DB is unreachable from internet — ever              ║
║  ✅ Every SG rule references a SG, not an IP            ║
╚══════════════════════════════════════════════════════════╝
```

</div>

---

## ⚒️ Components Reference Table

<div align="center">

| Category | Component | Placement / Configuration |
|:--------:|:---------:|:-------------------------:|
| 🏠 **Networking** | VPC | Single VPC `10.0.0.0/16` |
| | Subnets | 12 total — Public, Private App, Private DB |
| | Internet Gateway | Attached to VPC for public inbound/outbound |
| | NAT Gateway | In 1 Public Subnet, enables private → internet |
| | Route Tables | Public, Private App, Private DB (Isolated) |
| 💻 **Compute** | EC2 Frontend | Web Tier — behind Frontend ALB |
| | EC2 Backend | App Tier — behind Backend ALB |
| | Auto Scaling Groups | Dynamic scaling for both Frontend & Backend |
| 🔀 **Load Balancing** | Frontend ALB | Public Subnets — user entry point |
| | Backend ALB | Private Subnets — Web→App routing |
| 🗄️ **Database** | RDS Multi-AZ | Private DB Subnets — automatic failover |
| 🔐 **Security** | Security Groups | Chained least-privilege rules across all 5 tiers |
| | IAM Roles | EC2 access to CloudWatch, S3, Parameter Store |

</div>

---

## 🎓 Learning Outcomes

<div align="center">

```
┌─────────────────────────────────────────────────────────────┐
│  🎯  SKILLS DEMONSTRATED IN THIS PROJECT                    │
└─────────────────────────────────────────────────────────────┘
```

</div>

| # | Skill | Concept Mastered |
|---|-------|-----------------|
| 1 | 🏗️ **Multi-AZ VPC Design** | Spanning resources across AZs for HA |
| 2 | 🔀 **Subnet Tiering** | Public vs Private subnet isolation strategy |
| 3 | 🚪 **NAT vs IGW Routing** | Inbound/outbound patterns for each tier |
| 4 | 🔄 **ALB → EC2 → Backend Flow** | End-to-end load-balanced traffic tracing |
| 5 | 🔐 **Least Privilege SGs** | Chained security group referencing |
| 6 | 📈 **Auto Scaling** | Dynamic compute with Launch Templates |
| 7 | 🗄️ **Multi-AZ RDS** | Database high availability & failover |
| 8 | 🛣️ **Route Table Design** | Isolated DB routing with no default route |

---

## 📁 Folder Structure

```
aws-skill-builder-projects/
│
└── 📂 three-tier-architecture/
    │
    ├── 📄 README.md                   ← You are here
    │
    ├── 📂 diagrams/
    │   └── 🖼️  architecture.png        ← Full architecture diagram
    │
    ├── 📂 terraform/                  ← Infrastructure as Code (Terraform)
    │   ├── main.tf
    │   ├── variables.tf
    │   ├── outputs.tf
    │   └── modules/
    │       ├── vpc/
    │       ├── ec2/
    │       ├── alb/
    │       └── rds/
    │
    ├── 📂 cloudformation/             ← CloudFormation Templates
    │   ├── vpc-stack.yaml
    │   ├── compute-stack.yaml
    │   └── database-stack.yaml
    │
    ├── 📂 notes/                      ← Study notes & references
    │   └── architecture-decisions.md
    │
    └── 📂 future-projects/            ← Upcoming additions
        └── serverless-tier/
```

---

## 👤 Contact

<div align="center">



[![LinkedIn](https://img.shields.io/badge/LinkedIn-Arkan_Tandel-0A66C2?style=for-the-badge&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/arkan-tandel)
[![GitHub](https://img.shields.io/badge/GitHub-arkan--tandel-181717?style=for-the-badge&logo=github&logoColor=white)](https://github.com/arkan-tandel)
[![Email](https://img.shields.io/badge/Email-arkan%40example.com-D14836?style=for-the-badge&logo=gmail&logoColor=white)](mailto:arkan@example.com)

</div>

---

<div align="center">


![Footer](https://img.shields.io/badge/Made_with-☁️_AWS_&_❤️-FF9900?style=for-the-badge)
![AWS Skill Builder](https://img.shields.io/badge/AWS-Skill_Builder_Project-232F3E?style=for-the-badge&logo=amazonaws&logoColor=FF9900)

</div>
