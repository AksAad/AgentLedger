# AgentLedger — Product Requirements Document

**Document Status:** Draft  
**Product:** AgentLedger  
**Document Type:** Product Requirements Document  
**Primary Category:** AI Agent Governance, Runtime Authorization, Auditability, Enterprise AI Infrastructure

---

# 1. Product Overview

AgentLedger is an infrastructure platform that provides **identity, authorization, policy enforcement, human approval, execution tracking, and audit evidence for AI agents that interact with enterprise systems**.

An AI agent can increasingly perform real work through tools and integrations. A customer-success agent may read a CRM record, update an opportunity, create a task, or send an email. A finance agent may retrieve invoices, prepare a refund, or initiate a payment workflow. An internal engineering agent may access repositories, create pull requests, or deploy infrastructure.

The technical challenge is no longer simply giving an AI agent access to tools. Organizations need to control what an agent is permitted to do, understand who authorized that capability, stop actions that violate policy, route sensitive actions to a human, and maintain a trustworthy record of what happened.

AgentLedger sits between the agent and the systems the agent is trying to use.

The basic execution path is:

```text
Human User
    |
    v
AI Agent
    |
    v
AgentLedger
    |
    +--> Identity Resolution
    |
    +--> Policy Evaluation
    |
    +--> Risk Evaluation
    |
    +--> Approval Check
    |
    v
Authorized Tool Execution
    |
    v
Result
    |
    v
Audit + Evidence
```

AgentLedger provides the control layer that governs this interaction.

---

# 2. Problem Statement

Organizations are beginning to deploy AI agents that can perform actions using internal and external software systems.

Traditional application authorization generally operates around a human identity and an application or service credential.

For example:

```text
Employee → Salesforce
Employee → GitHub
Employee → Slack
Employee → Database
```

An autonomous agent introduces an additional actor:

```text
Employee
   |
   v
AI Agent
   |
   +--> Salesforce
   +--> Slack
   +--> GitHub
   +--> Database
```

The organization now needs to answer several questions for every meaningful agent action:

1. Which human initiated or delegated the action?
2. Which agent performed the action?
3. Which tool or integration was used?
4. What resource was accessed?
5. What operation was attempted?
6. Which policy governed the action?
7. What permissions did the user have?
8. What permissions did the agent have?
9. Was human approval required?
10. Who approved the action?
11. What actually happened?
12. Can the organization reconstruct the complete event later?

Existing enterprise systems provide pieces of this functionality, but organizations frequently have to combine identity systems, application permissions, API gateways, workflow systems, SIEM platforms, audit logs, and custom application logic.

This creates fragmented controls and incomplete visibility.

AgentLedger provides a unified runtime layer for agent actions.

---

# 3. Core Problem AgentLedger Solves

AgentLedger solves the problem of **controlling and proving AI-agent actions in enterprise environments**.

The central product question is:

> Before an AI agent performs an action, can the organization determine whether that action is allowed under the current identity, permissions, resource scope, policy, and risk context?

The second question is:

> After an action occurs, can the organization reconstruct exactly what happened and produce trustworthy evidence?

AgentLedger addresses both parts.

---

# 4. Target Users

## 4.1 Security Teams

Security teams need visibility into what AI agents can access and whether those actions comply with organizational policies.

Typical users:

- Security Engineers
- Application Security Engineers
- Security Operations
- AI Security Teams
- Security Architects

Typical needs:

- agent inventory
- tool inventory
- permissions
- policy enforcement
- blocked actions
- suspicious activity
- audit trails
- evidence exports

---

## 4.2 Platform Engineering Teams

Platform teams are responsible for providing infrastructure that developers can safely use.

Typical users:

- Platform Engineers
- Infrastructure Engineers
- Developer Productivity Teams
- AI Platform Teams

Typical needs:

- centralized agent registration
- tool registration
- reusable policies
- integration with MCP
- API-based authorization
- SDKs
- logs and observability

---

## 4.3 Compliance and Governance Teams

Compliance teams need evidence showing that AI-related activities are controlled and reviewable.

Typical users:

- Compliance Managers
- GRC Teams
- Internal Audit
- Risk Teams

Typical needs:

- audit evidence
- policy history
- approval history
- action records
- evidence exports
- control mapping

---

## 4.4 Engineering Teams Building Agents

Developers building agents need an easy way to enforce organizational policy without implementing authorization logic separately inside every agent.

Typical users:

- AI Engineers
- Backend Engineers
- Full-Stack Engineers
- ML Engineers

Typical needs:

- SDK
- API
- MCP middleware
- simple integration
- structured authorization decisions
- execution metadata

---

# 5. Where AgentLedger Can Be Used

AgentLedger can govern any AI agent that performs actions through APIs, tools, MCP servers, databases, SaaS applications, or internal services.

## Customer Success

A customer-success agent can:

- read account information
- retrieve customer activity
- update CRM records
- create follow-up tasks
- prepare customer emails

AgentLedger can enforce policies such as:

```text
Reading account information → allowed
Updating renewal date → allowed
Sending external email → human approval
Deleting account → denied
```

---

## Finance

A finance agent may:

- read invoices
- reconcile transactions
- prepare refunds
- create payment instructions
- update accounting records

Example policy:

```text
Refund <= ₹10,000 → automatically allowed
Refund > ₹10,000 → finance approval required
Refund > ₹100,000 → denied unless administrator overrides policy
```

---

## Sales

A sales agent may:

- read CRM opportunities
- update opportunity stages
- generate quotes
- send customer communications

AgentLedger can ensure that the agent cannot modify opportunities outside its assigned region or account scope.

---

## Engineering

An engineering agent may:

- read source repositories
- create branches
- create pull requests
- access CI systems
- modify deployment configuration

Example policy:

```text
Read repository → allowed
Create pull request → allowed
Merge pull request → approval required
Production deployment → approval required
Delete production resource → denied
```

---

## IT Operations

An IT agent may:

- inspect logs
- restart services
- modify configuration
- create tickets
- perform remediation

AgentLedger can control which environments and services the agent can touch.

---

## Healthcare and Regulated Environments

An agent may need access to sensitive information while being restricted by user role, purpose, resource type, and action.

AgentLedger can provide an enforcement and evidence layer around these interactions.

Actual regulatory compliance would remain the responsibility of the deploying organization and its compliance processes.

---

# 6. Product Vision

AgentLedger should become a standard control plane for AI-agent actions.

The long-term model is:

```text
Agent
  ↓
Identity
  ↓
Policy
  ↓
Risk
  ↓
Approval
  ↓
Tool Execution
  ↓
Evidence
```

Developers should be able to integrate AgentLedger into an existing agent without rebuilding the organization's authorization and audit infrastructure.

---

# 7. Product Principles

## 7.1 Deterministic Enforcement

Security-critical decisions must be deterministic and reproducible.

The policy engine must determine whether an action is allowed.

An LLM may assist with policy authoring, reasoning, investigation, classification, and summarization, but it must not be the final authority for security enforcement.

---

## 7.2 Complete Action Context

Every governed execution should carry enough context to establish:

```text
Who?
What agent?
Which tool?
Which resource?
Which operation?
Under which policy?
What risk?
What decision?
Who approved?
What happened?
When?
```

---

## 7.3 Least Privilege

Agents should receive only the access required for their assigned tasks.

Permissions should be scoped by:

- organization
- user
- agent
- tool
- resource
- operation
- environment
- conditions

---

## 7.4 Evidence by Default

Auditing should happen automatically during execution.

Developers should not have to manually create audit records after the fact.

---

## 7.5 Human Control for High-Risk Actions

Sensitive actions should be reviewable by humans before execution.

The product must make approvals fast and understandable.

---

# 8. Product Scope

The first version of AgentLedger will provide six major capabilities:

1. Agent Registry
2. Tool and MCP Registry
3. Runtime Authorization
4. Policy Engine
5. Human Approval
6. Audit and Evidence

These capabilities form the complete MVP.

---

# 9. User Journey

The complete product workflow is described below.

## Step 1 — Organization Setup

An administrator creates an organization.

Example:

```text
Organization:
Acme Technologies
```

The administrator can then create users and assign roles.

Initial roles:

```text
Admin
Security Admin
Developer
Viewer
Approver
```

---

## Step 2 — Register an Agent

A developer registers an agent.

Example:

```text
Name: Renewal Agent
Framework: LangGraph
Model: GPT-5.6
Environment: Production
Owner: Customer Success Engineering
```

The system generates an Agent ID.

---

## Step 3 — Register Tools

The administrator or platform engineer registers tools available to the agent.

Example:

```text
Salesforce.read
Salesforce.update
Slack.send
Email.send
```

Each tool receives metadata such as:

```text
Tool Name
Tool Type
Endpoint
Owner
Sensitivity
Environment
Supported Operations
```

---

## Step 4 — Create Policies

The administrator creates policies defining what the agent is allowed to do.

Example:

```yaml
agent: renewal-agent

rules:

  - tool: salesforce.read
    operation: read
    decision: allow

  - tool: salesforce.update
    operation: update
    resource: renewal-opportunity
    decision: allow

  - tool: email.send
    operation: send
    decision: approval_required

  - tool: salesforce.delete
    operation: delete
    decision: deny
```

Policies are versioned.

Example:

```text
sales-policy v1
sales-policy v2
sales-policy v3
```

Historical executions must retain the exact policy version used at the time.

---

# 10. Runtime Execution Sequence

This is the most important sequence in the entire product.

Suppose a customer-success agent decides to update a customer's renewal date.

The agent generates a tool request:

```json
{
  "tool": "salesforce.update",
  "resource": "account_123",
  "operation": "update",
  "arguments": {
    "renewal_date": "2027-04-01"
  }
}
```

The request is intercepted by AgentLedger.

### 10.1 Identity Resolution

AgentLedger identifies:

```text
Organization
Human user
Agent
Session
Environment
```

Example:

```text
Organization = Acme
User = Sarah
Role = Customer Success Manager
Agent = Renewal Agent
Environment = Production
```

---

### 10.2 Tool Validation

AgentLedger confirms that:

- the tool exists
- the tool is enabled
- the agent is registered
- the environment is valid

---

### 10.3 Permission Resolution

The system evaluates:

```text
User permissions
+
Agent permissions
+
Tool permissions
+
Resource scope
```

The action must satisfy all applicable restrictions.

---

### 10.4 Policy Evaluation

The policy engine evaluates the request.

Possible outcomes:

```text
ALLOW
DENY
APPROVAL_REQUIRED
```

Example:

```text
Tool: Salesforce.update
Resource: Renewal Opportunity
Operation: UPDATE

Policy:
Customer Success Policy v4

Decision:
ALLOW
```

---

### 10.5 Risk Evaluation

The system calculates a risk level based on deterministic factors.

Example:

```text
Operation: UPDATE
Data Sensitivity: Medium
Environment: Production
Agent Risk Tier: Medium
User Role: Customer Success
Resource: Renewal Opportunity

Risk:
MEDIUM
```

The exact scoring algorithm should be configurable.

---

### 10.6 Approval Check

If the policy requires approval:

```text
AgentLedger → Approval Queue
```

The action is paused.

The approver sees:

```text
Agent:
Renewal Agent

User:
Sarah

Requested Action:
Send renewal email

Target:
Acme Corporation

Reason:
Renewal date changed

Risk:
Medium

Policy:
Customer Communication Policy v2

Requested At:
04 Oct 2026 20:10
```

The approver can:

```text
Approve
Reject
```

The decision becomes part of the audit record.

---

### 10.7 Execution

After authorization or approval, AgentLedger forwards the request to the target tool.

Example:

```text
AgentLedger
    ↓
Salesforce API
```

AgentLedger records:

```text
Execution started
Execution completed
Response
Status
Latency
Failure reason if applicable
```

---

### 10.8 Evidence Creation

AgentLedger creates an evidence record.

Example:

```json
{
  "agent": "renewal-agent",
  "human": "sarah@acme.com",
  "tool": "salesforce.update",
  "resource": "account_123",
  "operation": "update",
  "policy": "customer-success-v4",
  "decision": "allow",
  "approval": null,
  "execution_status": "success",
  "timestamp": "2026-10-04T14:40:00Z"
}
```

The record is stored permanently according to the organization's retention policy.

---

# 11. Core Functional Requirements

## 11.1 Agent Registry

The system shall allow administrators to:

- create agents
- edit agents
- disable agents
- assign owners
- assign risk tiers
- assign environments
- view agent activity
- view connected tools
- view policies governing the agent

Required fields:

```text
Agent ID
Name
Description
Owner
Framework
Model
Version
Environment
Risk Tier
Status
Created At
Updated At
```

---

# 12. Tool Registry

The system shall maintain a centralized list of available tools.

Each tool should contain:

```text
Tool ID
Name
Description
Type
Endpoint
Provider
Environment
Sensitivity
Supported Operations
Status
```

Supported initial tool types:

```text
MCP
REST API
Internal API
Database
```

The architecture must allow additional connector types later.

---

# 13. MCP Support

MCP is a primary integration mechanism for the MVP.

AgentLedger should function as a governance layer around MCP tool execution.

Expected path:

```text
AI Agent
    ↓
MCP Client
    ↓
AgentLedger
    ↓
Policy Engine
    ↓
MCP Server
    ↓
External System
```

AgentLedger should capture the MCP tool name, arguments, identity context, policy decision, execution outcome, and timing.

The system should avoid requiring developers to rewrite their MCP servers.

---

# 14. Policy Engine

The policy engine is responsible for authorization decisions.

Every policy should have:

```text
Policy ID
Name
Version
Description
Rules
Status
Created By
Created At
Activated At
```

Policies must support:

### Identity conditions

```text
user.role
user.id
agent.id
organization.id
```

### Tool conditions

```text
tool.id
tool.type
```

### Resource conditions

```text
resource.type
resource.id
resource.owner
resource.region
```

### Operation conditions

```text
read
write
update
delete
send
execute
```

### Environment conditions

```text
development
staging
production
```

### Value conditions

```text
amount <= 10000
```

---

# 15. Policy Decision Model

Every request must result in exactly one primary decision:

```text
ALLOW
DENY
APPROVAL_REQUIRED
```

The system should also return:

```text
Policy ID
Policy Version
Matched Rules
Risk Level
Decision Reason
Decision Timestamp
```

Example:

```json
{
  "decision": "APPROVAL_REQUIRED",
  "policy_id": "policy_42",
  "policy_version": 3,
  "risk": "HIGH",
  "reason": "Payment exceeds configured approval threshold"
}
```

---

# 16. Human Approval System

Approval is required for sensitive operations.

The approval system shall provide:

```text
Approval ID
Execution ID
Requested By
Approver
Reason
Risk
Policy
Created At
Expiration
Status
Resolved At
```

Approval states:

```text
PENDING
APPROVED
REJECTED
EXPIRED
CANCELLED
```

A pending approval must prevent the underlying action from executing.

---

# 17. Audit System

Every governed action must create an audit event.

The audit system should capture at least:

```text
Event ID
Organization
User
Agent
Tool
Resource
Operation
Arguments metadata
Policy
Policy version
Risk
Decision
Approval
Execution status
Timestamp
Request ID
Session ID
```

Sensitive credentials and secrets must never be stored in plaintext.

---

# 18. Evidence Integrity

AgentLedger should support tamper-evident evidence.

The initial implementation can use hash chaining.

Example:

```text
Record 1
hash = SHA256(record1)

Record 2
hash = SHA256(record2 + hash(record1))

Record 3
hash = SHA256(record3 + hash(record2))
```

This creates a chain that can be verified later.

The product should expose an endpoint such as:

```http
GET /evidence/{id}/verify
```

Example result:

```json
{
  "valid": true,
  "chain_status": "verified",
  "verified_at": "2026-10-04T15:10:00Z"
}
```

This mechanism does not make the system universally tamper-proof. Production deployments would require additional controls such as append-only storage, access restrictions, key management, backups, and infrastructure-level controls.

---

# 19. Risk Engine

The first version should use deterministic scoring.

Example factors:

```text
Operation sensitivity
Data sensitivity
Environment
Agent risk tier
User privilege
Resource sensitivity
Financial value
External communication
```

Example scoring:

```text
LOW
MEDIUM
HIGH
CRITICAL
```

Risk should not automatically determine authorization.

Policy remains authoritative.

Risk is used to:

- inform policy decisions
- trigger approval workflows
- prioritize alerts
- identify anomalous activity

---

# 20. AI Components

AgentLedger itself may use AI for supporting functions.

## Policy Authoring Agent

Input:

```text
"Customer support agents can update account data but cannot delete accounts."
```

Output:

```yaml
tool: salesforce
operation: delete
resource: account
decision: deny
```

A human administrator must review and activate the generated policy.

---

## Evidence Analysis Agent

The agent can analyze large collections of audit records and summarize:

```text
Which agents performed unusual actions?
Which policies blocked the most requests?
Which agents generated the most approvals?
Which systems are accessed most frequently?
```

---

## Incident Investigation Agent

Given a suspicious action, the agent can trace related events and produce an investigation summary.

Example:

```text
Suspicious Event
 ↓
Agent Activity
 ↓
User Context
 ↓
Tool Calls
 ↓
Resource Access
 ↓
Policy Decisions
 ↓
Related Events
 ↓
Incident Summary
```

---

## Important AI Constraint

AI-generated recommendations must never directly bypass the deterministic authorization layer.

The authorization architecture must remain:

```text
Request
 ↓
Deterministic Policy Evaluation
 ↓
Decision
```

AI can assist before or after that decision.

---

# 21. Database Schema

AgentLedger should use PostgreSQL for transactional data.

## organizations

```sql
CREATE TABLE organizations (
    id UUID PRIMARY KEY,
    name TEXT NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

## users

```sql
CREATE TABLE users (
    id UUID PRIMARY KEY,
    organization_id UUID NOT NULL REFERENCES organizations(id),
    email TEXT NOT NULL,
    role TEXT NOT NULL,
    status TEXT NOT NULL,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

## agents

```sql
CREATE TABLE agents (
    id UUID PRIMARY KEY,
    organization_id UUID NOT NULL REFERENCES organizations(id),
    name TEXT NOT NULL,
    description TEXT,
    owner_id UUID REFERENCES users(id),
    framework TEXT,
    model TEXT,
    version TEXT,
    environment TEXT,
    risk_tier TEXT,
    status TEXT,
    created_at TIMESTAMP NOT NULL DEFAULT NOW(),
    updated_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

## tools

```sql
CREATE TABLE tools (
    id UUID PRIMARY KEY,
    organization_id UUID NOT NULL REFERENCES organizations(id),
    name TEXT NOT NULL,
    description TEXT,
    type TEXT NOT NULL,
    endpoint TEXT,
    environment TEXT,
    sensitivity TEXT,
    status TEXT,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

## policies

```sql
CREATE TABLE policies (
    id UUID PRIMARY KEY,
    organization_id UUID NOT NULL REFERENCES organizations(id),
    name TEXT NOT NULL,
    description TEXT,
    version INTEGER NOT NULL,
    rules JSONB NOT NULL,
    status TEXT NOT NULL,
    created_by UUID REFERENCES users(id),
    created_at TIMESTAMP NOT NULL DEFAULT NOW(),
    activated_at TIMESTAMP
);
```

## executions

```sql
CREATE TABLE executions (
    id UUID PRIMARY KEY,
    organization_id UUID NOT NULL REFERENCES organizations(id),
    agent_id UUID NOT NULL REFERENCES agents(id),
    user_id UUID REFERENCES users(id),
    tool_id UUID NOT NULL REFERENCES tools(id),
    session_id TEXT,
    request_id TEXT,
    operation TEXT NOT NULL,
    resource_type TEXT,
    resource_id TEXT,
    arguments JSONB,
    risk_level TEXT,
    policy_decision TEXT NOT NULL,
    status TEXT NOT NULL,
    started_at TIMESTAMP,
    completed_at TIMESTAMP,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

## approvals

```sql
CREATE TABLE approvals (
    id UUID PRIMARY KEY,
    execution_id UUID NOT NULL REFERENCES executions(id),
    requested_by UUID REFERENCES users(id),
    approver_id UUID REFERENCES users(id),
    status TEXT NOT NULL,
    reason TEXT,
    created_at TIMESTAMP NOT NULL DEFAULT NOW(),
    resolved_at TIMESTAMP
);
```

## evidence_records

```sql
CREATE TABLE evidence_records (
    id UUID PRIMARY KEY,
    execution_id UUID NOT NULL REFERENCES executions(id),
    policy_id UUID REFERENCES policies(id),
    evidence_type TEXT NOT NULL,
    payload JSONB NOT NULL,
    record_hash TEXT NOT NULL,
    previous_hash TEXT,
    created_at TIMESTAMP NOT NULL DEFAULT NOW()
);
```

---

# 22. API Design

## Authentication

```http
POST /auth/login
POST /auth/refresh
```

Authentication should be replaceable with OIDC/SSO in later versions.

---

## Agents

```http
POST /api/v1/agents
GET /api/v1/agents
GET /api/v1/agents/{id}
PATCH /api/v1/agents/{id}
POST /api/v1/agents/{id}/disable
```

---

## Tools

```http
POST /api/v1/tools
GET /api/v1/tools
GET /api/v1/tools/{id}
POST /api/v1/tools/{id}/test
```

---

## Policies

```http
POST /api/v1/policies
GET /api/v1/policies
GET /api/v1/policies/{id}
POST /api/v1/policies/{id}/validate
POST /api/v1/policies/{id}/activate
POST /api/v1/policies/{id}/deactivate
```

---

## Authorization

This is the core runtime API.

```http
POST /api/v1/authorize
```

Request:

```json
{
  "agent_id": "agent_123",
  "user_id": "user_456",
  "tool_id": "tool_789",
  "operation": "update",
  "resource_type": "customer_account",
  "resource_id": "account_1001",
  "context": {}
}
```

Response:

```json
{
  "decision": "ALLOW",
  "risk_level": "MEDIUM",
  "policy_id": "policy_123",
  "policy_version": 4,
  "reason": "Action permitted by Customer Success Policy v4"
}
```

---

## Execution

```http
POST /api/v1/execute
```

This endpoint should only execute requests that have passed authorization.

---

## Approvals

```http
GET /api/v1/approvals
GET /api/v1/approvals/{id}
POST /api/v1/approvals/{id}/approve
POST /api/v1/approvals/{id}/reject
```

---

## Audit

```http
GET /api/v1/audit/events
GET /api/v1/audit/events/{id}
GET /api/v1/audit/executions/{id}
```

---

## Evidence

```http
GET /api/v1/evidence
GET /api/v1/evidence/{id}
GET /api/v1/evidence/{id}/verify
POST /api/v1/evidence/export
```

---

# 23. Frontend Requirements

The dashboard should be designed for operational use rather than reporting alone.

## Main navigation

```text
Overview
Agents
Tools
Policies
Approvals
Executions
Audit
Evidence
Settings
```

---

## Overview

The overview page should show:

```text
Active Agents
Connected Tools
Actions Today
Blocked Actions
Pending Approvals
High-Risk Actions
Policy Violations
```

The page should provide direct access to the underlying records.

---

# 24. Agents Page

Each agent should display:

```text
Name
Owner
Environment
Risk Tier
Status
Tools
Policy Count
Actions
Last Active
```

Selecting an agent should show:

```text
Agent Overview
Permissions
Connected Tools
Policies
Recent Executions
Risk Events
```

---

# 25. Tools Page

Tools should be grouped by system.

Example:

```text
Salesforce
    read
    update
    create
    delete

Slack
    read
    send

GitHub
    read
    create_pr
    merge
```

Every tool should have a sensitivity classification.

---

# 26. Policies Page

The policy UI should allow administrators to:

- create policies
- edit policies
- validate policies
- compare versions
- activate versions
- deactivate policies
- inspect matched rules

The UI should provide a readable explanation of every rule.

---

# 27. Approval Queue

Approvers should see all pending actions.

Each request must clearly answer:

```text
Who requested it?
Which agent?
What action?
Which system?
Which resource?
Why?
Risk?
Which policy requires approval?
What will happen if approved?
```

The decision should be possible without opening multiple pages.

---

# 28. Execution Explorer

The execution explorer is one of the key interfaces.

Each record should provide a complete timeline:

```text
14:31:03
Agent requested action

14:31:03
Identity resolved

14:31:03
Policy evaluated

14:31:03
Risk calculated

14:31:03
Approval requested

14:31:41
Human approved

14:31:42
Tool executed

14:31:43
Execution succeeded

14:31:43
Evidence generated
```

This allows an engineer or auditor to reconstruct what happened.

---

# 29. Evidence Explorer

Evidence should be searchable by:

```text
Agent
User
Tool
Date
Policy
Decision
Risk
Environment
Organization
```

Users should be able to export evidence.

Initial export format:

```text
JSON
CSV
PDF
```

---

# 30. Security Requirements

AgentLedger itself will sit inside security-sensitive workflows, so it requires strong controls.

## Authentication

MVP:

```text
JWT
```

Future:

```text
OIDC
SAML
Enterprise SSO
```

---

## Authorization

AgentLedger APIs must use RBAC.

Example:

```text
Admin
  → all configuration

Security Admin
  → policies + audit

Developer
  → agents + tools

Approver
  → approvals

Viewer
  → read-only
```

---

## Secret handling

AgentLedger must not store raw credentials inside execution records.

Credentials should be stored using:

- environment secrets
- secret management systems
- encrypted storage

The logging layer must automatically redact:

```text
API keys
Tokens
Passwords
Authorization headers
Secrets
Sensitive personal data
```

---

# 31. Audit Requirements

Audit records should be:

- append-oriented
- timestamped
- associated with a unique request ID
- associated with an execution ID
- associated with a policy version
- protected from ordinary user modification

Administrative changes must also be audited.

Examples:

```text
Policy created
Policy modified
Policy activated
Agent created
Agent permissions changed
Tool enabled
Tool disabled
Approval granted
Approval rejected
```

---

# 32. Observability

AgentLedger should emit:

```text
Metrics
Logs
Traces
```

Important metrics:

```text
authorization_latency
policy_evaluation_latency
execution_latency
allowed_actions
denied_actions
approval_actions
failed_actions
```

Tracing should allow a request to be followed through:

```text
Agent
 →
AgentLedger
 →
Policy Engine
 →
Approval
 →
Tool
 →
Result
```

OpenTelemetry should be used where possible.

---

# 33. Initial Architecture

Recommended architecture:

```text
                    ┌──────────────────────┐
                    │      Next.js UI      │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │      FastAPI API     │
                    └──────────┬───────────┘
                               │
             ┌─────────────────┼─────────────────┐
             │                 │                 │
             ▼                 ▼                 ▼
      Identity Service   Policy Engine     Approval Service
             │                 │                 │
             └─────────────────┼─────────────────┘
                               ▼
                    ┌──────────────────────┐
                    │ Execution Gateway    │
                    └──────────┬───────────┘
                               │
                     ┌─────────┴─────────┐
                     ▼                   ▼
                   MCP                 REST
                 Servers                APIs
                     │                   │
                     └─────────┬─────────┘
                               ▼
                    ┌──────────────────────┐
                    │ Evidence / Audit     │
                    │ Engine               │
                    └──────────┬───────────┘
                               │
                               ▼
                    ┌──────────────────────┐
                    │     PostgreSQL       │
                    └──────────────────────┘
```

---

# 34. Recommended Tech Stack

## Backend

```text
Python
FastAPI
Pydantic
SQLAlchemy
```

## Database

```text
PostgreSQL
```

## Cache / Messaging

```text
Redis
```

Redis should be introduced only where asynchronous jobs, approval queues, rate limiting, or distributed coordination require it.

---

## Policy

Initial implementation:

```text
OPA / Rego
```

A custom lightweight policy abstraction can sit above OPA if needed.

---

## AI

```text
LangGraph
OpenAI-compatible APIs
Ollama
```

The model provider should be configurable.

---

## Frontend

```text
Next.js
TypeScript
Tailwind CSS
shadcn/ui
```

---

## Infrastructure

```text
Docker
Docker Compose
Nginx
OpenTelemetry
```

Kubernetes should be introduced only after the single-node deployment has been proven.

---

# 35. Repository Structure

Recommended monorepo:

```text
agentledger/
│
├── apps/
│   ├── api/
│   └── web/
│
├── services/
│   ├── policy-engine/
│   ├── execution-gateway/
│   ├── approval-service/
│   ├── evidence-service/
│   └── agent-service/
│
├── packages/
│   ├── sdk-python/
│   ├── sdk-typescript/
│   └── shared-types/
│
├── integrations/
│   ├── mcp/
│   ├── rest/
│   └── database/
│
├── policies/
│   └── examples/
│
├── infrastructure/
│   ├── docker/
│   └── otel/
│
├── tests/
│   ├── policy/
│   ├── security/
│   ├── integration/
│   └── e2e/
│
├── docs/
│
├── docker-compose.yml
├── README.md
└── LICENSE
```

The services should remain modular, but the MVP should be deployable as a simple Docker Compose environment.

---

# 36. Development Sequence

The product should be developed in a strict dependency order.

## Phase 1 — Domain Model

Define the core entities:

```text
Organization
User
Agent
Tool
Policy
Execution
Approval
Evidence
```

Write the database schema and API contracts first.

Deliverable:

```text
Database schema
OpenAPI specification
Entity relationships
```

---

## Phase 2 — Authentication and Organizations

Implement:

```text
Organization creation
User creation
Login
JWT
Roles
```

Deliverable:

A user can authenticate and access only their organization.

---

## Phase 3 — Agent and Tool Registry

Implement:

```text
Agent CRUD
Tool registration
Agent-tool relationships
```

Deliverable:

An administrator can register an agent and assign available tools.

---

## Phase 4 — Policy Engine

Implement:

```text
Policy creation
Policy versioning
Policy validation
Policy activation
Authorization evaluation
```

Deliverable:

The system can reliably return:

```text
ALLOW
DENY
APPROVAL_REQUIRED
```

for test requests.

This is the first major technical milestone.

---

## Phase 5 — Execution Gateway

Create the runtime interception layer.

Flow:

```text
Agent
 ↓
Authorization
 ↓
Policy
 ↓
Approval
 ↓
Tool
```

Deliverable:

A real MCP tool call can be intercepted and governed.

---

## Phase 6 — Approval System

Implement:

```text
approval request
approval queue
approve
reject
expiry
audit
```

Deliverable:

A high-risk tool request can be paused, reviewed, approved, and then executed.

---

## Phase 7 — Audit

Capture every significant event.

Deliverable:

A developer can search for an execution and reconstruct its complete lifecycle.

---

## Phase 8 — Evidence

Implement:

```text
hash chain
verification
evidence export
policy version association
```

Deliverable:

An administrator can produce a verifiable evidence record for an execution.

---

## Phase 9 — Dashboard

Build the UI after the backend workflow works end-to-end.

The dashboard should expose the system's existing operational data.

Deliverable:

A security administrator can understand:

```text
What agents exist?
What can they do?
What are they doing?
What has been blocked?
What requires approval?
What happened?
```

---

## Phase 10 — AI Features

Only after deterministic enforcement works should the AI capabilities be added.

Sequence:

```text
Policy Analyst
 ↓
Evidence Analyst
 ↓
Incident Investigator
 ↓
Behavioral Risk Analysis
```

Each AI feature should consume existing structured data.

---

# 37. MVP Definition

AgentLedger MVP is complete when the following workflow works end-to-end:

```text
1. Register organization
2. Create user
3. Register AI agent
4. Register MCP tool
5. Create policy
6. Connect agent to tool
7. Agent attempts tool call
8. AgentLedger receives request
9. Identity is resolved
10. Policy is evaluated
11. Risk is calculated
12. Action is allowed, denied, or paused
13. Human approves when required
14. Tool executes
15. Execution result is recorded
16. Evidence record is generated
17. Evidence integrity can be verified
18. Administrator can inspect the complete event
```

If this sequence works reliably, the MVP has achieved its primary purpose.

---

# 38. MVP Acceptance Criteria

## Authorization

Given a registered agent with permission to read a resource:

```text
Request → ALLOW
```

Given an agent without permission:

```text
Request → DENY
```

Given an action requiring approval:

```text
Request → APPROVAL_REQUIRED
```

The tool must not execute until approval is granted.

---

## Audit

For every execution there must be a record containing:

```text
Agent
User
Tool
Operation
Resource
Policy
Decision
Timestamp
Outcome
```

---

## Evidence

For every completed execution:

```text
Evidence record exists
Hash exists
Previous hash is linked where applicable
Verification endpoint succeeds
```

---

## Security

The system must:

```text
Prevent cross-organization access
Prevent unauthorized policy changes
Prevent ordinary users from modifying audit records
Redact secrets from logs
Require authorization before tool execution
```

---

# 39. Performance Requirements

Initial MVP targets:

```text
Policy evaluation: <50 ms target
Authorization API: <100 ms target excluding downstream systems
Dashboard API: <500 ms target for normal queries
```

The exact target should be benchmarked under realistic workloads.

The system should be horizontally scalable once the MVP architecture has been validated.

---

# 40. Reliability Requirements

For security-sensitive actions:

- authorization failures should fail closed
- policy service outages should not silently permit protected actions
- approval state should survive service restarts
- audit records should be durably persisted
- execution IDs should be unique
- duplicate requests should be detectable

A failed authorization dependency should result in a safe failure state for governed actions.

---

# 41. Initial Integrations

Build only two integrations for the MVP:

## MCP

Required.

It demonstrates that AgentLedger can govern modern agent tool usage.

## REST API

Required.

It proves the architecture is not limited to MCP.

Additional integrations can follow:

```text
Slack
GitHub
Salesforce
PostgreSQL
Jira
Google Workspace
Microsoft 365
```

Do not build all of them initially.

---

# 42. Example End-to-End Scenario

A customer-success organization deploys a Renewal Agent.

The agent is connected to:

```text
Salesforce
Slack
Email
```

Its business task is:

> Identify accounts approaching renewal, update CRM records, notify internal owners, and draft customer communication.

The organization configures:

```text
Salesforce.read → ALLOW
Salesforce.update → ALLOW
Slack.send → ALLOW
Email.send → APPROVAL_REQUIRED
Salesforce.delete → DENY
```

The agent identifies a renewal opportunity.

It attempts:

```text
Salesforce.update
```

AgentLedger checks:

```text
User
Agent
Tool
Resource
Policy
Risk
Environment
```

The action is allowed.

The agent then attempts:

```text
Email.send
```

AgentLedger determines that external email requires approval.

The action is paused.

A Customer Success Manager receives the request.

The manager approves it.

AgentLedger executes the email operation.

The complete action chain is stored:

```text
User
 ↓
Agent
 ↓
Intent
 ↓
Tool
 ↓
Policy
 ↓
Risk
 ↓
Approval
 ↓
Execution
 ↓
Result
 ↓
Evidence
```

A month later, an auditor asks:

> "Show me every external communication performed by AI agents and prove that approvals were obtained where required."

AgentLedger can search the execution records and produce the corresponding evidence.

That is the business value of the system.

---

# 43. Business Value

AgentLedger creates value in four areas.

## Risk reduction

Organizations gain control over agent capabilities and sensitive actions.

---

## Operational efficiency

Developers do not need to independently implement authorization, approval workflows, and audit logging for every agent.

---

## Visibility

Security teams obtain centralized visibility across agents and tools.

---

## Audit readiness

Organizations can reconstruct agent actions and generate structured evidence.

---

# 44. Business Model

Initial product strategy:

## Developer / Community Tier

Free:

```text
Self-hosted
Limited agents
Limited executions
Basic policies
MCP support
Basic audit
```

---

## Team Tier

Paid:

```text
More agents
More executions
Advanced policies
Approvals
Evidence exports
Team management
Extended retention
```

---

## Enterprise

Features:

```text
SSO
Private deployment
Advanced identity
SIEM integrations
Long-term retention
Custom policies
Dedicated support
Compliance evidence
Advanced analytics
```

Pricing should ultimately be tied to measurable usage such as governed executions, active agents, or organization size.

---

# 45. Initial Customer Profile

The first realistic customer should be a company that:

```text
Already uses AI agents
Has multiple internal tools
Has a security or platform engineering function
Needs centralized governance
Has enough technical maturity to integrate an SDK/API
```

An ideal early adopter could be:

```text
50–500 employee SaaS company
AI-heavy engineering organization
Using MCP / APIs / internal agents
No dedicated AI governance infrastructure
```

This customer profile is small enough for direct outreach and technically mature enough to understand the problem.

---

# 46. Product Metrics

The most important metrics are:

## Adoption

```text
Active organizations
Active agents
Connected tools
```

## Usage

```text
Governed executions
Executions per agent
```

## Security

```text
Blocked actions
Approval-required actions
Policy violations
Unauthorized attempts
```

## Operational efficiency

```text
Average approval time
Authorization latency
Execution failure rate
```

## Evidence

```text
Evidence generated
Evidence verification success
Audit reconstruction time
```

A strong product-level metric would be:

> **Percentage of agent actions governed through AgentLedger.**

---

# 47. Risks

## Risk 1 — Large vendors enter the category

Microsoft, IBM, cloud providers, cybersecurity companies, and enterprise automation vendors can build similar functionality.

Response:

Focus the early product on:

```text
developer experience
MCP
self-hosting
open-source adoption
simple policy configuration
excellent auditability
```

---

## Risk 2 — Enterprises already have IAM and SIEM

Companies may ask:

> "Why can't our existing IAM and SIEM solve this?"

The answer must come from the product architecture.

IAM determines identities and permissions.

SIEM records events and detects incidents.

AgentLedger governs the **runtime action context of AI agents** and connects identity, agent, tool, policy, approval, execution, and evidence into one event.

The product should integrate with IAM and SIEM rather than trying to replace them.

---

## Risk 3 — Authorization bugs

A false allow could have significant consequences.

Mitigation:

- deterministic policies
- deny-by-default
- extensive tests
- policy simulation
- policy versioning
- staged deployments
- clear execution traces

---

## Risk 4 — Excessive complexity

The product could become a large enterprise platform before achieving product-market fit.

Mitigation:

Maintain the MVP around:

```text
Agent
Tool
Policy
Authorization
Approval
Execution
Evidence
```

---

# 48. Features to Build First

Priority order:

```text
P0
Database
Authentication
Agent Registry
Tool Registry
Policy Engine
Authorization API
Execution Gateway
Audit

P1
Human Approval
MCP Integration
Evidence Verification
Dashboard

P2
AI Policy Assistant
AI Evidence Analysis
Incident Investigation
Additional Integrations
```

---

# 49. Features to Cut From MVP

The following should not be part of the initial release:

```text
Agent marketplace
Workflow builder
20+ integrations
Mobile app
Custom LLM
Advanced anomaly detection
Automated remediation
Kubernetes operator
Multi-region deployment
Billing infrastructure
Large compliance framework library
Complex analytics
```

They can be revisited after the core runtime governance loop is stable.

---

# 50. Eight-Week Execution Plan

## Week 1

Build the domain model and PostgreSQL schema.

Deliver:

```text
Organizations
Users
Agents
Tools
Policies
Executions
Approvals
Evidence
```

Write the OpenAPI contracts.

---

## Week 2

Build:

```text
Authentication
RBAC
Organizations
Agent Registry
Tool Registry
```

By the end of the week, an administrator should be able to create an agent and register tools.

---

## Week 3

Build the policy engine.

Implement:

```text
ALLOW
DENY
APPROVAL_REQUIRED
```

Create automated policy tests.

---

## Week 4

Build the execution gateway.

The first complete runtime path should work:

```text
Agent
 ↓
AgentLedger
 ↓
Policy
 ↓
Tool
```

Support REST first if it accelerates testing.

Then add MCP.

---

## Week 5

Build human approval.

The complete path should become:

```text
Agent
 ↓
Policy
 ↓
Approval
 ↓
Execution
```

---

## Week 6

Build audit and evidence.

Implement:

```text
Execution timeline
Hash chaining
Evidence generation
Verification
Export
```

At this point the product should be demonstrable.

---

## Week 7

Build the dashboard:

```text
Overview
Agents
Tools
Policies
Approvals
Executions
Evidence
```

Focus on operational clarity.

---

## Week 8

Harden and deploy.

Work on:

```text
Security testing
Failure handling
Performance benchmarks
Docker deployment
Documentation
Example agents
Example policies
Demo environment
```

---

# 51. Testing Strategy

Testing must focus heavily on authorization correctness.

## Unit Tests

Test:

```text
Policy parsing
Policy evaluation
Risk calculation
RBAC
Hash generation
Evidence verification
```

---

## Integration Tests

Test:

```text
Agent → Gateway
Gateway → Policy
Gateway → MCP
Gateway → REST
Approval → Execution
Execution → Evidence
```

---

## Security Tests

Attempt:

```text
Unauthorized tool access
Cross-tenant access
Privilege escalation
Policy bypass
Direct tool access
Replay
Credential leakage
Audit tampering
```

---

## End-to-End Test

A complete automated test should:

```text
Create organization
Create user
Create agent
Create tool
Create policy
Send tool request
Require approval
Approve action
Execute tool
Generate evidence
Verify evidence
```

This should run in CI.

---

# 52. Demo Environment

The repository should include a complete local demo.

Example:

```text
Acme Corporation
```

Agents:

```text
Renewal Agent
Finance Agent
Engineering Agent
```

Tools:

```text
Mock Salesforce
Mock Slack
Mock Email
Mock Database
```

Policies:

```text
Customer Success Policy
Finance Policy
Engineering Production Policy
```

The developer should be able to start the entire system with:

```bash
docker compose up
```

The demo should immediately show the complete runtime governance flow.

---

# 53. Success Criteria

AgentLedger should be considered successful when:

1. A developer can integrate an agent through the AgentLedger API or SDK.
2. The system can govern MCP and REST tool calls.
3. Policies can produce deterministic authorization decisions.
4. High-risk actions can be paused for human approval.
5. Every execution creates an audit record.
6. Every execution can be reconstructed from the audit system.
7. Evidence records can be verified for integrity.
8. Multiple organizations remain logically isolated.
9. The complete system can run locally using Docker.
10. An external developer can understand and integrate the system using the documentation.

---

# 54. Long-Term Roadmap

Once the MVP proves the core concept, the product can expand into:

## Phase 2 — Developer Platform

```text
Python SDK
TypeScript SDK
MCP middleware
framework integrations
policy simulation
local development tools
```

---

## Phase 3 — Enterprise Identity

```text
OIDC
SAML
SCIM
Okta
Entra ID
Google Workspace
```

---

## Phase 4 — Security Intelligence

```text
behavioral baselines
anomaly detection
agent drift detection
tool abuse detection
automated investigations
```

---

## Phase 5 — Governance and Compliance

```text
SOC 2 evidence mapping
ISO 27001 evidence mapping
AI governance controls
policy libraries
evidence automation
```

---

## Phase 6 — Enterprise Control Plane

```text
multi-region deployments
SIEM integrations
enterprise policy management
centralized agent inventory
organization-wide risk analytics
```

---

# 55. Final Product Definition

AgentLedger is a runtime governance and evidence system for AI agents.

A company installs or integrates AgentLedger between its AI agents and the systems those agents can access.

When an agent attempts an action, AgentLedger establishes the identity context, determines the requested operation and resource, evaluates the organization's policies, calculates risk, determines whether human approval is required, and then either blocks, approves, or executes the action.

After execution, AgentLedger records the complete event and generates evidence that can be inspected or exported later.

The system therefore creates a complete lifecycle:

```text
REGISTER
    ↓
AUTHORIZE
    ↓
POLICY CHECK
    ↓
RISK CHECK
    ↓
APPROVAL
    ↓
EXECUTE
    ↓
AUDIT
    ↓
VERIFY
    ↓
ANALYZE
```

The core abstraction is an **agent action**.

An agent action consists of:

```text
Actor
Agent
Tool
Operation
Resource
Context
Policy
Decision
Approval
Execution
Outcome
Evidence
```

That abstraction should remain at the center of the entire product.

AgentLedger's first job is to make agent actions **controlled, traceable, and reviewable**.

Its longer-term role is to become the infrastructure through which organizations safely operate autonomous AI systems across their software environment.

---

# 56. One-Sentence Product Definition

> **AgentLedger is the runtime control and evidence layer that governs what AI agents can do, pauses sensitive actions for human approval, and maintains a verifiable record of every governed action.**

---

# 57. MVP Development Target

The first shippable product should be small enough that one engineer can operate and understand the entire system.

The target MVP is:

```text
Next.js Dashboard
        +
FastAPI
        +
PostgreSQL
        +
Policy Engine
        +
MCP Gateway
        +
Approval Service
        +
Audit/Evidence Engine
        +
Python SDK
```

The complete MVP workflow is:

```text
AI Agent
   ↓
AgentLedger SDK / MCP Gateway
   ↓
Identity
   ↓
Policy
   ↓
Risk
   ↓
ALLOW / DENY / APPROVAL_REQUIRED
   ↓
Human Approval when required
   ↓
Tool Execution
   ↓
Execution Record
   ↓
Evidence Record
   ↓
Verification
```

That workflow is the product.

Everything else should be added only when it strengthens that workflow or solves a customer problem discovered through real usage.
