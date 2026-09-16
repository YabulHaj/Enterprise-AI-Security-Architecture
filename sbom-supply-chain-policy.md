# Enterprise Software Supply Chain & SBOM Management Policy

**Purpose:** Establish rigorous visibility, tracking, and risk remediation standards for all third-party software dependencies, open-source libraries, and container images entering the enterprise ecosystem. 

Aligned with Executive Order 14028 (Improving the Nation's Cyber Security) and NIST SP 800-218 (Secure Software Development Framework - SSDF).

## 1. Software Bill of Materials (SBOM) Generation
- [ ] **Mandatory Artifact Generation:** All internally developed software applications, microservices, and container images must automatically generate an SBOM (in SPDX or CycloneDX format) during the CI/CD build pipeline.
- [ ] **Third-Party Vendor Procurement:** All commercial off-the-shelf (COTS) and third-party software vendors must provide a machine-readable SBOM upon contract intake. Software missing an SBOM is blocked from staging environments.
- [ ] **Repository Centralization:** All generated and acquired SBOMs are ingested and stored in a centralized, searchable repository for immediate cross-referencing during vulnerability disclosures.

## 2. Continuous Dependency Scanning & Triage
- [ ] **Automated Pipeline Gates:** Dependency-check and Software Composition Analysis (SCA) tools are integrated into early CI/CD stages to block builds containing known critical vulnerabilities (CVSS 9.0+).
- [ ] **Container Image Hygiene:** Base container images are pulled exclusively from signed, trusted enterprise registries. Automated scans run weekly against all active container repositories.
- [ ] **Transitive Dependency Tracking:** Scanning tools must inspect nested (transitive) dependencies, as vulnerabilities frequently hide deep within third-party component trees.

## 3. Vulnerability Remediation SLAs
- [ ] **Critical Open-Source Vulnerabilities:** Publicly exploited or critical severity components must be patched, upgraded, or mitigated via compensating controls within **48 hours** of notification.
- [ ] **High-Severity Vulnerabilities:** Remediated or mitigated within **14 calendar days**.
- [ ] **Exception Process:** Any waiver or deferral of a critical vulnerability patch requires formal risk sign-off from the Information Security Officer (ISO) and a defined expiration date.

## 4. Supply Chain Integrity & Provenance
- [ ] **Artifact Signing:** All deployment artifacts, container images, and release packages must be cryptographically signed (e.g., using Cosign / Sigstore) to guarantee integrity before deployment to production.
- [ ] **Cryptographic Verification:** Production orchestration platforms (e.g., Kubernetes admission controllers) are configured to reject unsigned or unverified images automatically.

---
*Created and maintained by Yazan Abul-Haj.*
