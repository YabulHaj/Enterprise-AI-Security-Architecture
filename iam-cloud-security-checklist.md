# Enterprise Cloud & IAM Security Checklist

**Purpose:** A practical, zero-fluff checklist for securing enterprise cloud infrastructure, establishing Identity and Access Management (IAM) baselines, and maintaining compliance with core governance frameworks. 

Built for environments where speed is necessary, but security is non-negotiable.

## 1. Identity & Access Management (IAM) Baseline
- [ ] **Enforce MFA Universally:** Multi-Factor Authentication is required for all users, with hardware keys (FIDO2) prioritized for administrative and root accounts.
- [ ] **Zero Trust / Least Privilege:** Roles and permissions are scoped strictly to the resources required for a specific job function. No persistent admin access.
- [ ] **Disable Inactive Accounts:** Automated scripts are in place to disable accounts that have been inactive for 30+ days.
- [ ] **Service Account Audits:** Service accounts and API keys are regularly rotated, have explicit expiration dates, and are isolated from human user groups.
- [ ] **SSO Integration:** Centralized Single Sign-On (SAML/OIDC) is implemented for all enterprise applications to reduce password fatigue and centralize termination workflows.

## 2. Cloud Security Architecture (AWS / Multi-Cloud)
- [ ] **Lock Down Root Accounts:** Cloud provider root accounts are secured with hardware MFA, removed from any active management roles, and strictly monitored for login attempts.
- [ ] **Network Segmentation:** Cloud resources are segmented using Virtual Private Clouds (VPCs), subnets, and strict security groups to limit the blast radius of any potential breach.
- [ ] **Public Bucket Prevention:** S3 buckets (or equivalent object storage) have block public access enabled at the account level. 
- [ ] **Encryption Everywhere:** Data is encrypted at rest (using KMS or native cloud encryption) and in transit (TLS 1.2+ minimum).

## 3. Governance, Risk & Compliance (GRC) Logging
- [ ] **Centralized Audit Trails:** CloudTrail, network flow logs, and IAM activity are piped into a centralized, tamper-proof logging environment (SIEM).
- [ ] **Log Retention Policies:** Logs are retained according to industry compliance standards (e.g., 1 year hot, 3+ years cold storage).
- [ ] **Automated Alerting:** High-risk actions (e.g., modifying security groups, disabling MFA, root account logins) trigger immediate alerts to the IT security team.

---
*Created and maintained by Yazan Abul-Haj.*
