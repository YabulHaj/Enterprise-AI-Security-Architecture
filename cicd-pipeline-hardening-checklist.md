# The 5-Minute CI/CD Pipeline Hardening Checklist

**Why this matters today:**
We spend millions locking down AWS production environments and building zero-trust architectures, only to leave the CI/CD pipeline wide open. Attackers don't bother brute-forcing your production servers anymore. Instead, they push a malicious payload to a weakly protected repository or compromise a third-party action. Your own automated pipeline then kindly builds, packages, and deploys their malware directly into your infrastructure with god-level privileges. 

Pipeline poisoning is the silent killer of modern DevOps. If you aren't treating your CI/CD pipeline as a Tier-0 production asset, you are handing over the keys to the kingdom.

Designed for immediate, frictionless implementation. Aligned with NIST SP 800-53 (SA-8 Security Engineering Principles) and CIS Controls v8.

## 1. Code & Commit Integrity (Stop Malicious Injection)
- [ ] **Enforce Branch Protection:** Block direct pushes to `main` or `production` branches. Require a minimum of one approved pull request (PR) from a separate, authenticated user before merging.
- [ ] **Require Commit Signing:** Enable strict cryptographic commit signing (GPG, SSH, or Sigstore). Configure the repository to automatically reject unsigned commits to ensure non-repudiation.
- [ ] **Pin Third-Party Actions to SHA:** Stop using mutable tags (e.g., `@v2`) for external pipeline steps. Pin all external actions to a specific, immutable commit SHA to prevent upstream poisoning.

## 2. Runner & Compute Hygiene (Contain the Blast Radius)
- [ ] **Use Ephemeral Runners:** Configure build agents/runners to be ephemeral (single-use). Destroy the container or VM immediately after a job completes to prevent lateral movement or credential harvesting.
- [ ] **Isolate Pipeline Environments:** Never run build jobs in the same VPC as your production data. Isolate the pipeline compute layer and strictly filter outbound egress traffic to an explicit allow-list.
- [ ] **Disable Unverified Extensions:** Audit and explicitly block the installation of unapproved marketplace extensions or plugins within the CI/CD environment.

## 3. Secret & Identity Management (Kill Hardcoded Credentials)
- [ ] **Rip Out Static Credentials:** Remove all long-lived IAM keys and static API tokens from pipeline variables. 
- [ ] **Implement OIDC Federation:** Use OpenID Connect (OIDC) to grant the pipeline short-lived, Just-in-Time (JIT) access tokens scoped strictly to the current deployment task.
- [ ] **Enable Secret Scanning:** Turn on native secret scanning to block any PR that contains an API key, private key, or password *before* it merges into the codebase.

---
*Created and maintained by Yazan Abul-Haj.*
