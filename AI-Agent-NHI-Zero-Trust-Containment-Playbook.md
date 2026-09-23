# Definitive Reference Architecture: Autonomous AI Agent & NHI Containment

## The Systemic Failure
We spent a decade wrestling shadow IT, and now we are explicitly granting persistent, unconstrained API access to autonomous scripts that fundamentally hallucinate. 

Right now, enterprise engineering teams are wiring LLMs directly to internal APIs, databases, and CI/CD pipelines. To make these autonomous agents "work seamlessly," developers are assigning them legacy service accounts with broad `ReadOnlyAccess` or even `AdministratorAccess`. 

You are treating a non-deterministic AI agent like a trusted internal microservice. 

When your RAG-enabled support bot gets prompt-injected via an external user input, it won't just output a funny response. It will use its underlying IAM role to list, query, and exfiltrate the contents of your internal S3 buckets. 

This architecture establishes a definitive, zero-trust cryptographic boundary for Non-Human Identities (NHIs) and AI Agents, ensuring that even if an agent is hijacked, its blast radius is completely neutralized.

## The Zero-Trust Agent Boundary (Core Directives)

To align with national critical infrastructure standards for zero-trust and AI risk management, all autonomous agents must operate under the following architectural constraints:

1. **No Long-Lived Credentials:** AI agents must never possess static API keys. All agent authorization must be federated via short-lived, Just-In-Time (JIT) tokens (e.g., OIDC or SPIFFE/SPIRE).
2. **Deterministic Output Scoping:** An agent's IAM permissions must be strictly scoped to the exact specific resources it needs for a single task execution, utilizing tag-based access control (ABAC).
3. **The "Human-in-the-Loop" (HITL) Gateway:** Any destructive action (Write, Delete, Update) initiated by an AI agent must trigger an asynchronous cryptographic approval request to a human administrator. 
4. **Mandatory API Gateway Rate-Limiting:** Agents operate at machine speed. Without strict API Gateway throttling, a looping agent will unintentionally DDoS your own internal services or generate a $50k LLM token bill in 12 hours.

---

## Policy-as-Code: The Agent Containment IAM Profile

Below is a production-ready AWS IAM policy baseline for an AI Agent. 
**What this does:** It restricts the agent to accessing *only* data explicitly tagged for AI consumption, forces the session to originate from a designated secure VPC, and strictly limits the session duration.

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AllowOnlyAIClearedS3Objects",
      "Effect": "Allow",
      "Action": [
        "s3:GetObject"
      ],
      "Resource": "arn:aws:s3:::YOUR-AI-DATA-BUCKET/*",
      "Condition": {
        "StringEquals": {
          "s3:ExistingObjectTag/DataClassification": "AI-Cleared-Public"
        }
      }
    },
    {
      "Sid": "AllowOnlyAIClearedDynamoDBTables",
      "Effect": "Allow",
      "Action": [
        "dynamodb:Query"
      ],
      "Resource": [
        "arn:aws:dynamodb:*:*:table/YOUR-AI-DATA-TABLE",
        "arn:aws:dynamodb:*:*:table/YOUR-AI-DATA-TABLE/index/*"
      ],
      "Condition": {
        "StringEquals": {
          "aws:ResourceTag/DataClassification": "AI-Cleared-Public"
        }
      }
    },
    {
      "Sid": "DenyRequestsOutsideApprovedVPCEndpoint",
      "Effect": "Deny",
      "Action": [
        "s3:*",
        "dynamodb:*"
      ],
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:SourceVpce": "vpce-REPLACE_WITH_APPROVED_ENDPOINT_ID"
        },
        "Bool": {
          "aws:PrincipalIsAWSService": "false"
        }
      }
    }
  ]
}
```
