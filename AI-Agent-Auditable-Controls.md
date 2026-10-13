# Auditable AI Agent Controls

**A control catalog and evidence guide for governing AI agents and other non-human identities in enterprise cloud environments.**

> **Status: Draft v0.1 (October 2026). Not peer reviewed. Mappings are the author's interpretation, not official NIST or OWASP guidance. Verify every control ID against the current published documents before relying on it. Review and corrections are welcome (see [Contributing](#contributing)).**

**Author:** Yazan Abul-Haj. Background in information security and quality auditing (ISO 27001 / ISO 9001), including internal audit programs and corrective-action tracking.
**License:** MIT

---

## 1. The problem

Organizations are giving AI agents the ability to read data, call internal APIs, change configurations, and trigger pipelines. Security teams usually protect these agents with the same tools they use for ordinary service accounts.

An auditor will ask five questions about any agent in production:

1. **Who is it?** Does each agent have its own identity, and who owns it?
2. **What may it do?** Is its authority limited to what the task needs?
3. **Who approved the risky actions?** Can a human approval be shown for each high-impact action?
4. **What did it do?** Is there a tamper-resistant record of every action and the input that triggered it?
5. **How is it stopped?** Is there a tested way to revoke it quickly?

Many organizations cannot yet answer these with evidence. This document turns the five questions into 12 controls. Each control comes with a mapping to U.S. frameworks, a test an auditor can run, and the evidence to keep.

**What this document is:** a practical control catalog for security, risk, and audit teams.
**What it is not:** a certification, a guarantee of security, or a replacement for your organization's own risk assessment.

## 2. Who should use this

| Reader | Use it to |
|---|---|
| CISO / risk executive | Answer "are our agents governed?" with a pass/fail view (Section 3) |
| GRC / internal audit | Test each control and collect evidence (Section 5) |
| Cloud / platform engineer | Turn each control into a technical requirement (Section 6) |

## 3. Executive readiness check (10 minutes)

Answer yes or no for every AI agent in production. Any "no" is a finding.

- [ ] Every agent is on an inventory with a named human owner. (AG-01)
- [ ] No agent uses a shared or long-lived credential. (AG-02)
- [ ] No agent has wildcard (`*`) permissions. (AG-03)
- [ ] High-impact actions need a recorded human approval. (AG-04)
- [ ] You can show a log of what each agent did last week. (AG-08)
- [ ] Someone has tested how to disable an agent within a defined time. (AG-12)

## 4. Control catalog and framework mapping

Framework references: **NIST SP 800-53 Rev. 5** (security and privacy controls), **NIST AI RMF 1.0** (functions: Govern, Map, Measure, Manage), **OWASP Top 10 for LLM Applications (2025)**.

| ID | Control | NIST SP 800-53 Rev. 5 | NIST AI RMF function | OWASP LLM (2025) |
|---|---|---|---|---|
| AG-01 | Inventory every agent; assign a named owner | CM-8 | Govern | LLM06 |
| AG-02 | Unique identity per agent; short-lived credentials only | IA-5, IA-9, AC-2 | Govern, Manage | LLM06 |
| AG-03 | Least-privilege permissions, no wildcards | AC-3, AC-6 | Manage | LLM06 |
| AG-04 | Human approval for high-impact actions; approver differs from requester | AC-5, AC-6 | Manage | LLM06 |
| AG-05 | Treat external input as untrusted; limit what injected text can trigger | SI-10, SC-7 | Map, Manage | LLM01, LLM05 |
| AG-06 | Rate, quota, and spend limits | SC-5 | Manage | LLM10 |
| AG-07 | Restrict outbound network access to an allow-list | SC-7 | Manage | LLM02 |
| AG-08 | Log every agent action with identity, input, policy decision, and result | AU-2, AU-3, AU-12 | Measure | LLM06 |
| AG-09 | Protect log integrity; define retention | AU-9, AU-11 | Measure | n/a |
| AG-10 | Review logs and alert on abnormal agent behavior | SI-4, AU-6, CA-7 | Measure, Manage | LLM06 |
| AG-11 | Control changes to agent prompts, tools, and permissions | CM-2, CM-3 | Govern, Manage | LLM03 |
| AG-12 | Incident response plan and tested kill switch for agents | IR-4 | Manage | LLM06 |

Also consider third-party model and tool provenance (800-53 SR-3, SR-4; OWASP LLM03). It is not a separate control here because this version focuses on runtime governance. Contributions welcome.

## 5. Audit test procedures and evidence

For each control: what the auditor does, and what is kept as evidence.

| ID | Test procedure | Evidence to retain |
|---|---|---|
| AG-01 | Compare the agent inventory to actual deployed agents; confirm each has an owner | Inventory export; list of unowned agents |
| AG-02 | List credentials used by agents; confirm no shared accounts and no static keys older than the policy limit | Credential report; policy |
| AG-03 | Review each agent's permissions; flag wildcards and unused permissions | Permission export; reviewer sign-off |
| AG-04 | Sample 10 high-impact actions; confirm each has a recorded approval from a different person | Approval records matched to action logs |
| AG-05 | Review how external text reaches the agent; confirm the agent cannot trigger destructive actions from that text alone | Architecture diagram; test results |
| AG-06 | Confirm limits exist and test that exceeding them stops the agent | Configuration; test log |
| AG-07 | Review egress rules; confirm only approved destinations are reachable | Firewall or proxy rules; test results |
| AG-08 | Pick a random agent action; trace it end to end in the logs | Sample trace with all required fields |
| AG-09 | Confirm logs cannot be edited by the agent or its owner; confirm retention matches policy | Storage settings; retention policy |
| AG-10 | Confirm alerts exist and show evidence that someone reviewed one | Alert rules; ticket or review record |
| AG-11 | Sample changes to prompts, tools, or permissions; confirm each was reviewed and approved | Change tickets; approvals |
| AG-12 | Run a drill: disable one agent and measure time to stop | Drill report with time taken |

**Rating each control:** Not implemented / Partially implemented / Implemented and evidenced. Only the last rating counts as a pass.

## 6. Technical implementation notes

Plain-language requirements an engineer can act on:

- **Identity (AG-02):** use the cloud provider's short-lived credential mechanism (for example, federated tokens that expire in minutes) instead of stored keys.
- **Permissions (AG-03):** grant only specific actions on specific resources. Tag-based conditions can limit an agent to data labeled as cleared for AI use.
- **Approval (AG-04):** the system should *block* a high-impact action until the approval exists. A notification after the fact is not an approval.
- **Egress (AG-07):** route all outbound traffic through a proxy with an allow-list.
- **Logs (AG-09):** write logs to storage the agent cannot modify, using write-once settings where available.

## 7. Limitations

- Mappings are interpretive. A control can map to more than one framework item; this table shows the primary ones.
- These controls reduce risk. They do not make an agent safe. Prompt injection has no complete fix today.
- No control here has been validated through a real third-party audit.
- Cloud-provider details change. Check provider documentation for current settings.

## 8. Contributing

This is a draft meant to be improved by practitioners.

- **Found an incorrect mapping?** Open an issue with the control ID and the correct reference.
- **Used this in an audit or assessment?** Tell us what worked and what did not (remove any confidential details).
- **Want to add a control or a provider-specific guide?** Open a pull request.

---

## Appendix A. Example approval rule (illustrative, unverified)

> **Status: this example has not been run or tested by the author. Do not use it in production. It shows the *idea* of AG-04 only. If you run it, please report the result in an issue.**

The rule below is written for Open Policy Agent (OPA), a tool that evaluates access rules. It allows reads of AI-cleared data, and allows changes only when a different person has approved and the approval has not expired.

```rego
package agent.authz

import rego.v1

default allow := false

# Reads of data labeled as cleared for AI use
allow if {
    input.action in {"read", "list"}
    input.resource.classification == "ai-cleared"
}

# High-impact actions need a live approval from someone other than the agent
allow if {
    input.action in {"write", "update", "delete"}
    input.approval.approved_by != ""
    input.approval.approved_by != input.agent.id
    time.now_ns() < input.approval.expires_ns
}
```

## Appendix B. Reference frameworks

- NIST SP 800-53 Rev. 5, *Security and Privacy Controls for Information Systems and Organizations*
- NIST AI Risk Management Framework (AI RMF 1.0)
- OWASP Top 10 for LLM Applications (2025)

*Check each publisher's site for the current version.*
