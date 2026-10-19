# Enterprise Risk Register & Compliance Matrix (FinTech Mock Case Study)

## Objective
This repository contains a structured IT Risk Register evaluating a cloud-hosted financial technology application against ISO/IEC 27001 controls and the Digital Personal Data Protection (DPDP) Act.

| Risk ID | Asset / Process | Threat / Vulnerability | Inherent Risk | Mitigating Technical Control | ISO 27001 Control | DPDP Act Alignment | Residual Risk |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **RISK-01** | Customer Database (AWS RDS) | Unencrypted personally identifiable information (PII) at rest; vulnerable to data theft. | **High** | Enforce AWS KMS using AES-256 cryptographic encryption for all database clusters. | A.8.24 (Use of cryptography) | Sec. 8 (Obligations of Data Fiduciary to protect PII) | **Low** |
| **RISK-02** | Administrative Portals | Lack of Multi-Factor Authentication (MFA) on employee endpoints leading to credential stuffing. | **High** | Implement Identity & Access Management (IAM) conditional access requiring hardware MFA tokens. | A.5.15 (Access control) | Sec. 8(5) (Reasonable security safeguards) | **Low** |
| **RISK-03** | Third-Party Payment API | Vendor does not provide data localization proof, storing data outside permissible jurisdictions. | **Medium** | Execute a comprehensive Vendor Risk Assessment questionnaire and enforce data localization clauses in SLAs. | A.5.19 (Information security in supplier relationships) | Sec. 15 (Cross-border transfer restrictions) | **Negligible** |
