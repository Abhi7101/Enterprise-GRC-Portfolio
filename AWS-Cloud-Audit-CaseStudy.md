# Technical Security Audit & Framework Alignment Case Study
**Target Environment:** AWS-Hosted Multi-Tenant FinTech Application (Node.js API + PostgreSQL RDS)
**Auditor:** Abhishek Hajare, M.Tech Cyber Security

---

## 1. Executive Summary & Scope
This audit evaluates the staging and production infrastructure of a microservices-based financial platform against **ISO/IEC 27001:2022** and the **Digital Personal Data Protection (DPDP) Act, 2023**. 

The scope includes:
* AWS Identity and Access Management (IAM) configurations
* Amazon RDS (PostgreSQL) data tier storage
* Network edge architecture (VPC, Security Groups, WAF)

---

## 2. Technical Vulnerability Analysis & Mitigation Log

### Finding 01: Insecure Data Storage & Lack of Isolation (Cryptographic Failures)
*   **Technical Evidence:** Inspection of the AWS CloudFormation template (`template.json`, line 142) revealed that the Amazon RDS instance `fintech-prod-db` was deployed with the parameter `"KmsKeyId": "default"`. This relies on AWS managed keys instead of Customer Managed Keys (CMK), limiting audit trail visibility. Furthermore, storage volume encryption was explicitly set to `false` on the attached EBS snapshots.
*   **Regulatory Risk (DPDP Act Sec. 8):** Storing financial transaction summaries and customer PII in unencrypted block storage constitutes a failure to implement "reasonable security safeguards," exposing the entity to statutory penalties up to ₹250 Crores.
*   **Compliance Gap (ISO 27001:2022 A.8.24):** Non-compliant with cryptographic control objectives.
*   **Remediation Executed:** 
    1. Provisioned a custom AWS KMS symmetric key with a strict key policy limiting rotation and usage permissions to the database service role.
    2. Modified the Terraform script to enforce encryption at rest:
       ```hcl
       resource "aws_db_instance" "prod_db" {
         allocated_storage    = 100
         engine               = "postgres"
         instance_class       = "db.r6g.xlarge"
         storage_encrypted    = true
         kms_key_id           = aws_kms_key.db_encryption_key.arn
       }
       ```

### Finding 02: Permissive Network Edge Configuration (Excessive Privilege)
*   **Technical Evidence:** Analysis of Security Group `sg-0fa712b3921` attached to the internal payment processing EC2 instances revealed an inbound rule allowing `0.0.0.0/0` on port `22` (SSH) and port `5432` (PostgreSQL). 
*   **Compliance Gap (ISO 27001:2022 A.8.20, A.8.22):** Network security management failure; improper segregation of networks.
*   **Remediation Executed:** Revoked global ingress rules. Reconfigured the security group to strictly accept traffic originating exclusively from the Application Load Balancer (ALB) security group on port `8080`, and forced all SSH traffic to route through a designated AWS Systems Manager (SSM) Bastion Host.

---

## 3. DPDP Act Data Flow & Localization Audit Matrix
To satisfy Section 15 of the DPDP Act regarding cross-border data restrictions, the data pipeline architecture was mapped:

