# Enterprise Server Hardening & Patch Management Policy

**Purpose:** Establish a standardized, secure baseline for all enterprise servers (Windows and Linux) and enforce a strict, SLA-driven patch management lifecycle to mitigate vulnerabilities. 

Aligned with CIS Benchmarks and NIST SP 800-40 (Guide to Enterprise Patch Management Technologies).

## 1. System Hardening Baselines (CIS Aligned)
- [ ] **Disable Unused Services:** All non-essential services, ports, and protocols (e.g., Telnet, FTP) are disabled by default on the base image.
- [ ] **SSH & Remote Access:** Direct root login via SSH is permanently disabled. SSH access requires key-based authentication (no password authentication) and is restricted to jump servers/bastion hosts.
- [ ] **Admin Privilege Restriction:** Default administrator or root account names are renamed or disabled where supported.
- [ ] **Endpoint Detection & Response (EDR):** The enterprise standard EDR agent is baked into the base image and verified active before the server is joined to the production domain or network.
- [ ] **Time Synchronization:** All servers are configured to sync with authorized enterprise NTP servers to ensure accurate log correlation.

## 2. Patch Management SLAs & Lifecycle
- [ ] **Environment Phasing:** All patches are deployed in a phased approach: Development -> Staging -> Production. Direct-to-production patching is strictly forbidden unless classified as an Emergency Out-of-Band (OOB) release.
- [ ] **Critical Vulnerabilities (Zero-Day/Actively Exploited):** Emergency change control board (CAB) approval bypassed. Deployed to production within **48 hours** of vendor release.
- [ ] **High-Severity Patches (CVSS 7.0 - 8.9):** Deployed to production within **14 days** of release.
- [ ] **Medium/Low Patches:** Rolled into the standard monthly maintenance window (within **30 days**).
- [ ] **Automated Rollbacks:** Snapshot or backup automation is verified prior to patch execution to ensure a sub-15-minute rollback capability if production services degrade.

## 3. Continuous Vulnerability Management
- [ ] **Automated Scanning:** Authenticated vulnerability scans are executed weekly against all production assets.
- [ ] **Configuration Drift Monitoring:** Automated tools monitor server configurations against the approved CIS baseline image, triggering alerts if unauthorized changes occur.
- [ ] **Decommissioning Process:** Servers inactive for 45 days are automatically isolated, stripped of network access, and flagged for permanent decommissioning to prevent "zombie" vulnerabilities.

---
*Created and maintained by Yazan Abul-Haj.*
