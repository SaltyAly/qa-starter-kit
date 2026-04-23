# QA Starter Kit — Design Spec
**Date:** 2026-04-23  
**Repo:** https://github.com/SaltyAly/qa-starter-kit

---

## Problem

A developer running a multi-service open source platform (FINOS ecosystem, compliance/risk/safety context) has:
- More services than can be manually QA'd without process rigor
- A QA agent built from an AI-generated system prompt with no methodology behind it
- Existing specs, ADRs, and code — QA is being invited in after the fact

They need a QA starter kit that gives them process, structure, and a better agent — something grounded in the compliance frameworks their ecosystem already uses.

---

## Target User

A developer (not a QA specialist) working on a multi-repo open source SDK platform. They know their services, have documented decisions (ADRs), and have already built a rough Claude QA agent. They need methodology, not just more tooling.

---

## What We're Building

Three standalone markdown files. Each is independently useful. Order = priority.

```
qa-starter-kit/
├── 01-qa-agent-spec.md        ← replaces the system prompt
├── 02-qa-onboarding.md        ← what to do first when invited in
└── 03-api-service-checks.md   ← per-service checklist template
```

---

## Ecosystem Context

This kit is written for projects operating in or adjacent to:
- **FINOS** — Fintech Open Source Foundation (financial services compliance)
- **OpenSSF OSPS Baseline** — Open Source Project Security Baseline, 39 controls across 7 categories
- **Gemara** — GRC Engineering Model for Automated Risk Assessment; connects compliance requirements to technical evidence
- **PVTR/Privateer** — automated scanner for OSPS Baseline controls

The OSPS Baseline categories are the compliance backbone for Piece 3:
- AC: Access Control
- BR: Build & Release
- DO: Documentation
- GV: Governance
- LE: Legal
- QA: Quality Assurance
- SA/VM: Security Assessment & Vulnerability Management

---

## Piece 1: QA Agent Spec (`01-qa-agent-spec.md`)

Replaces an AI-generated system prompt with a methodology-grounded agent definition.

### Sections

**Role definition**  
Risk-first reviewer. Job is to find what breaks and assess impact — not to confirm things work. Always labels findings by risk tier. Never produces unlabeled output.

**Project context block** *(developer fills in)*  
- List of services
- Location of specs/ADRs
- Auth model used across the platform
- Compliance frameworks in scope (OSPS Baseline, FINOS, etc.)

**Risk tiers**  
- High: auth, financial data, PII, external integrations, compliance-rule violations
- Medium: contract mismatches, missing error handling, undocumented behavior
- Low: non-breaking inconsistencies, cosmetic API issues

**What it checks (priority order)**  
1. OSPS Baseline compliance signals (per category: AC, BR, DO, GV, LE, QA, SA/VM)
2. Auth and access (tokens, scopes, failure behavior)
3. Spec/ADR compliance (does behavior match documented decisions?)
4. Error behavior (fails safely, informatively, without leaking internals)
5. FINOS/compliance signals (PII handling, audit trails, financial safety checks)

**Finding format** *(every flag must use this)*  
```
Service: [name]
Risk: High / Medium / Low
OSPS Control: [ID if applicable, e.g. AC-01]
Finding: [one sentence — what's wrong]
Expected: [what the spec/ADR says should happen]
Actual: [what's happening instead]
Suggested action: fix / investigate / document as known gap
```

**Escalation logic**  
- High-risk findings on auth or financial data: stop and escalate, do not continue
- Medium/Low: flag and continue, do not block

---

## Piece 2: QA Onboarding Checklist (`02-qa-onboarding.md`)

What to do the first time you're invited into a project with existing specs, ADRs, and code. Written for a developer, not a QA specialist — every step includes a one-line reason.

### Four Phases

**Phase 1 — Read before touching anything**
- Read all ADRs: understand the WHY behind decisions
- Read service specs: what each service should do and what's out of scope
- Map the auth model: how services authenticate with each other and with users
- Map what data flows through and what's sensitive (PII, financial, compliance-relevant)
- Map service dependencies: what calls what
- Check for existing OSPS Baseline assessment results (Security Insights file, PVTR output)

**Phase 2 — Assess risk before writing a single check**
- Which services are external-facing? (higher risk)
- Which services touch financial data or compliance rules? (highest risk — FINOS context)
- Which services have zero tests? (coverage gap — flag immediately)
- Which services have no Security Insights file or OSPS Baseline coverage?
- What's the blast radius if a service fails?
- Output: a ranked list of services to test first

**Phase 3 — Set up your environment**
- Identify dev/staging environments
- Find test credentials and sandbox configs
- Find the logging stack — where do errors surface?
- Know what can be tested in isolation vs. needs full integration

**Phase 4 — First checks**
- Start with the top 2-3 highest-risk services only (risk-based, not exhaustive)
- Every check is written against the spec or ADR — not assumptions
- Document expected vs. actual behavior explicitly, every time

---

## Piece 3: API Service Check Template (`03-api-service-checks.md`)

Per-service checklist. Two layers: OSPS Baseline compliance first, then API/integration behavior.

### Layer 1 — OSPS Baseline Checks (per service/repo)

**AC — Access Control**
- Branch protection in place?
- Workflow permissions appropriately scoped?
- Auth/authorization controls documented and enforced?

**BR — Build & Release**
- CI/CD inputs sanitized?
- Secret scanning active?
- Release artifacts signed or attested (SLSA)?
- HTTPS enforced on all release endpoints?

**DO — Documentation**
- Vulnerability disclosure process documented?
- Dependency management policy documented?
- User/integration guide exists?

**GV — Governance**
- Contribution guidelines present?
- Code review requirements documented and enforced?

**LE — Legal**
- License file present and valid?
- License consistent across releases?

**QA — Quality Assurance**
- Dependency management automated or regularly reviewed?
- Code review required before merge?
- Binary files restricted from repo?

**SA/VM — Security Assessment & Vulnerability Management**
- Threat model documented?
- Vulnerability disclosure policy present?
- Security contact listed?
- Private vulnerability reporting enabled?

### Layer 2 — API/Integration Checks (per endpoint/service)

**Auth & Access**
- Does this service require auth? What type?
- Invalid/expired token → 401 (not 500)
- Token missing required scope → 403 (not silent success or wrong data)
- Tokens in headers, not URLs or query strings
- Rate limiting behaves correctly when exceeded

**Contract / Spec Compliance**
- Endpoint matches spec: request shape, response shape, status codes
- Required fields enforced; optional fields handled correctly when absent
- Behavior matches its ADR — no undocumented deviations

**Error Handling**
- Malformed input → 400, not 500
- Dependency unavailable → graceful failure, not silent hang
- Error messages are useful but don't leak internals (stack traces, service names, infra details)
- Errors are logged and findable

**FINOS / Compliance Signals**
- PII and financial data not appearing in logs or error responses
- Sensitive operations have an audit trail
- Compliance rules from ADRs are enforced in the service
- Financial operations have safety checks: limits, validations, boundary conditions

**Integration Points**
- Service correctly calls its dependencies
- Handles unexpected downstream responses gracefully
- Webhooks/callbacks validated before being acted on
- Cross-service data matches on both sides of the integration

---

## Tone & Format Rules

- Written for a developer, not a QA specialist
- Every check item has a one-line reason if it isn't obvious
- No QA jargon without a plain-language explanation
- Checklists over prose wherever possible
- OSPS Baseline control IDs included so findings can be traced back to the framework

---

## Delivery

Files go into the root of `SaltyAly/qa-starter-kit`. The README gets updated to explain what each file is and how to use them together.
