# Cryptographic SBOM Enforcement

### No Proof. No Production.

We spend billions protecting production networks.

Then we download software from the internet and **trust it because the build succeeded.**

That is the supply-chain gap.

A compromised dependency does not need to break through your firewall.

Your CI/CD pipeline can build it, sign it, and deploy it for the attacker.

## The rule

**An artifact does not enter production unless it can prove what it is, where it came from, and what is inside it.**

### The enforcement chain

```text
SOURCE
  ↓
TRUSTED BUILD
  ↓
ARTIFACT DIGEST
  ↓
SBOM
  ↓
VULNERABILITY CHECK
  ↓
SIGNED PROVENANCE
  ↓
SIGNED SBOM / ATTESTATION
  ↓
ADMISSION CONTROL
  ↓
PRODUCTION
```

Every production artifact must pass:

**1. SBOM exists**
CycloneDX or SPDX.

**2. Artifact is signed**
The signature covers the exact artifact digest.

**3. SBOM is bound to that artifact**
No swapping an approved SBOM onto a different image.

**4. Vulnerability policy passes**
Critical/High findings block deployment unless an explicit, time-limited exception exists.

**5. Provenance is trusted**
The system verifies who built it and from which source.

**6. Production verifies again**
The admission controller rejects anything that cannot prove compliance.

And one more rule:

> **A dependency that has been abandoned for 24 months triggers architectural review.**

Not because age automatically makes software unsafe.

Because **unmaintained software is a supply-chain decision—not a dependency update.**

## The interesting part

This is not another vulnerability scanner.

The scanner tells you:

> “There is a vulnerability.”

The enforcement layer asks:

> **“Can this exact artifact prove that it is allowed to run?”**

That changes security from **advice** into **control.**

```text
UNKNOWN  →  BLOCK
UNSIGNED →  BLOCK
UNTRUSTED PROVENANCE → BLOCK
FAILED POLICY → BLOCK
VALIDATED → ALLOW
```

### Minimal CI gate

```yaml
name: Supply Chain Zero Trust

on:
  pull_request:
    branches: [main, production]

jobs:
  security-gate:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Build
        run: |
          docker build -t "$IMAGE:$GITHUB_SHA" .

      - name: Generate SBOM
        uses: anchore/sbom-action@v0.24.0
        with:
          image: "$IMAGE:$GITHUB_SHA"
          format: cyclonedx-json
          output-file: artifact-sbom.json

      - name: Scan SBOM
        uses: anchore/scan-action@v7.4.0
        with:
          sbom: artifact-sbom.json
          fail-build: true
          severity-cutoff: high

      - name: Install Cosign
        uses: sigstore/cosign-installer@v4.1.2

      - name: Sign artifact
        run: |
          cosign sign --yes "$IMAGE:$GITHUB_SHA"

      - name: Attest SBOM
        run: |
          cosign attest \
            --yes \
            --type cyclonedx \
            --predicate artifact-sbom.json \
            "$IMAGE:$GITHUB_SHA"
```

The critical production rule is simple:

> **Do not trust the tag. Verify the immutable digest and its attestations.**

Kubernetes admission can enforce signed images and attestations before workloads are admitted.

**Software should not enter production because someone clicked “deploy.”**

**It should enter because it proved it deserves to.**

# No proof. No production.
