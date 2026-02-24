# Design and Evaluate an AWS Solution Using the Well-Architected and Cloud Adoption Frameworks

## Lab Scenario
An organization is migrating a two-tier web application (frontend + backend database) from on-premises servers to AWS. Management wants the migration to align with AWS best practices and follow a well-architected design from day one.

---

## Task 1 — Review the Existing Architecture

### 1.1 Workload Components (Identified)
- **Frontend web application tier** (web server / app server that serves the UI and handles HTTP requests)
- **Backend database tier** (database server storing application data)
- **Network connectivity between tiers** (application-to-database communication)
- **Internet access** (end users accessing the frontend from the public internet)

### 1.2 Potential Risks / Weaknesses (Noted)
- **Single point of failure** if frontend or database runs on a single instance/AZ
- **No high availability** (no load balancer, no Multi-AZ database)
- **No stated backup/DR strategy** (risk of data loss and prolonged downtime)
- **Security exposure risk** (e.g., overly permissive security groups, public DB access)
- **Limited observability** (no centralized monitoring/logging defined)
- **Manual deployments/operations** (higher operational risk and inconsistency)

---

## Task 2 — Evaluate Using the AWS Well-Architected Framework (WAF)

### WAF Assessment Table

| Pillar | Observation (Strength) | Improvement Recommendation | Supporting AWS Service / Feature |
|---|---|---|---|
| Operational Excellence | Clear separation of responsibilities between frontend and database tiers | Automate provisioning, deployments, and operational visibility (monitoring + alarms) | AWS CloudFormation, Amazon CloudWatch |
| Security | Two-tier design can reduce direct DB exposure if properly segmented | Apply least privilege, secure network boundaries, and encrypt data at rest/in transit | IAM, VPC Security Groups/NACLs, AWS KMS, ACM |
| Reliability | Simple architecture is easy to understand and troubleshoot | Implement redundancy and automated failover across Availability Zones | Application Load Balancer, Auto Scaling, Amazon RDS Multi-AZ |
| Performance Efficiency | Workload can start small and scale as needed | Enable horizontal scaling and use managed database performance features | Auto Scaling Groups, RDS instance sizing + storage options |
| Cost Optimization | Small initial footprint can limit early cost | Right-size resources, use autoscaling, and monitor spend continuously | AWS Cost Explorer, AWS Budgets, Compute Optimizer |

#### Notes
The recommendations focus on introducing automation, segmentation, availability, scalability, and cost governance while keeping the architecture appropriate for a two-tier web application.

---

## Task 3 — Apply the AWS Cloud Adoption Framework (CAF)
(Approximately 150–200 words per perspective)

### 3.1 Business Perspective
The organization has a clear motivation to migrate to AWS and wants best practices applied from the start, which indicates strong alignment between cloud adoption and business intent. However, business readiness typically requires measurable outcomes such as cost targets, performance expectations, availability SLAs, and a defined migration timeline. To improve readiness, leadership should define success metrics (e.g., uptime, response time, recovery targets, cost ceilings), assign executive sponsorship, and create a migration roadmap with milestones. Establishing a business case and mapping cloud decisions to business priorities (customer experience, risk reduction, faster delivery) will ensure the migration remains outcome-driven rather than purely technical.

### 3.2 People Perspective
The migration will require cloud skills that may not exist in an on-prem-focused team. Likely gaps include AWS networking (VPC design), IAM security design, infrastructure as code, monitoring, and incident response in cloud environments. To increase readiness, the organization should implement targeted AWS training, assign clear roles (cloud architect, security lead, operations lead), and create knowledge sharing routines such as internal demos and documentation. Identifying “cloud champions” and establishing pairing between experienced and learning engineers reduces operational risk. A defined responsibility model will also help the team adopt AWS’s shared responsibility concept and reduce misconfigurations during the migration.

### 3.3 Governance Perspective
Cloud migration without governance often results in access sprawl, inconsistent tagging, unclear ownership, and uncontrolled cost growth. Governance readiness should include standards for account structure, resource tagging, approvals, auditability, and cost allocation. Key enablers include defining policies for IAM role use, security baselines, naming conventions, and mandatory tags (Owner, Environment, CostCenter). The organization should also set up cost guardrails and review practices (budgets, alerts, periodic cost reviews). Governance should address compliance needs, change management, and traceability of infrastructure changes. Implementing these guardrails early helps ensure the migration remains secure, compliant, and financially controlled.

### 3.4 Platform Perspective
The platform perspective focuses on the technical foundation to run workloads reliably. The two-tier app is suitable for AWS, but it needs a standard landing zone design: a well-structured VPC with public and private subnets, routing, and security boundaries. Readiness actions include selecting a target architecture pattern (ALB + Auto Scaling for frontend; RDS Multi-AZ for database), defining infrastructure-as-code for consistency, and ensuring environment separation (dev/test/prod). The organization should also standardize how secrets are managed, how configurations are deployed, and how patching is performed. Building the platform foundation first reduces rework later and makes scaling and operations far more predictable.

### 3.5 Security Perspective
Security readiness requires adopting least privilege, strong network segmentation, encryption, logging, and continuous monitoring. For the two-tier app, the database should not be public, and only the application tier should communicate with it on required ports. IAM roles should replace long-lived credentials where possible. Data at rest should be encrypted using KMS, and TLS should be used in transit. Logging and alerting should be enabled to detect abnormal behavior. Security perspective also includes understanding AWS shared responsibility and ensuring security processes (reviews, approvals, incident response) are defined. These steps reduce the most common migration risks: misconfigured access, exposed services, and insufficient visibility.

### 3.6 Operations Perspective
Operational readiness means being able to run and support the workload efficiently. The current implied design lacks defined monitoring, incident response, backup validation, and deployment discipline. The organization should establish baseline operational processes: monitoring dashboards, alarms for availability/latency, log centralization, backup and restore testing, and a basic on-call/incident playbook. Automation should be used for deployments and infrastructure provisioning to reduce manual errors. Defining SLOs and operational metrics will make it easier to verify that the migration achieved its goals. These enablers improve uptime, reduce mean time to recovery, and ensure the system can be supported long term.

---

## Task 4 — Improved AWS Architecture (Description)

### 4.1 Proposed Revised Architecture (Two-Tier, Well-Architected)
**Networking**
- One **VPC** spanning **two Availability Zones**
- **Public subnets**: ALB and NAT (if needed)
- **Private subnets**: application instances and database

**Frontend / Application Tier**
- **Application Load Balancer (ALB)** in public subnets
- **EC2 Auto Scaling Group** in private subnets across two AZs
- Optional: **AWS Systems Manager** for patching/maintenance

**Database Tier**
- **Amazon RDS (Multi-AZ)** in private subnets
- Automated backups + snapshots enabled
- Encryption at rest via **AWS KMS**

**Security**
- Security Groups:
  - ALB allows inbound HTTPS (443) from the internet
  - App instances allow inbound only from ALB
  - RDS allows inbound only from app instances on DB port
- IAM roles for EC2 and services (least privilege)
- TLS certificates via **AWS Certificate Manager (ACM)**

**Observability & Operations**
- **Amazon CloudWatch** metrics, logs, alarms (CPU, latency, 5xx, RDS health)
- Centralized log retention policy and alerting

**Infrastructure as Code**
- Use **AWS CloudFormation** (or Terraform) to provision resources consistently

### 4.2 How This Addresses the Five WAF Pillars
- **Operational Excellence**: monitoring + automation via CloudWatch/CloudFormation
- **Security**: least privilege IAM, private DB, encryption, TLS
- **Reliability**: Multi-AZ for app and DB + health checks
- **Performance Efficiency**: scaling via Auto Scaling + appropriate sizing
- **Cost Optimization**: right-sizing, autoscaling, budgets/cost monitoring

---

## Reflection (≈150 words)
This lab strengthened my ability to evaluate cloud workloads using structured AWS frameworks. Applying the Well-Architected Framework helped me identify practical strengths and weaknesses across operations, security, reliability, performance, and cost, rather than focusing only on infrastructure. The Cloud Adoption Framework showed that successful migration depends on organizational readiness, including skills, governance, and operational processes, not just technical design. I learned how to propose improvements that are aligned with best practices (such as Multi-AZ resilience, least privilege access, encryption, and automation) while keeping the architecture realistic for a two-tier web application. Overall, the exercise improved my confidence in communicating architecture decisions clearly and defensibly, using framework-based reasoning that a cloud architect would be expected to apply.
