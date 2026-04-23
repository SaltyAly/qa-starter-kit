# QA Starter Kit Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build three standalone markdown files (agent spec, onboarding checklist, API service checks) that give a developer entering a multi-service FINOS/OpenSSF project the process and structure they need to QA it rigorously.

**Architecture:** Three independently useful files — ordered by urgency. File 1 replaces the developer's AI-generated agent system prompt with a methodology-grounded spec. File 2 gives them a phase-by-phase onboarding process. File 3 gives them a per-service check template that layers OSPS Baseline compliance on top of API behavior checks.

**Tech Stack:** Markdown only. No code dependencies. Delivered to GitHub repo root.

---

### Task 1: Write `01-qa-agent-spec.md`

**Files:**
- Create: `01-qa-agent-spec.md` (repo root)

- [ ] **Step 1: Write the file**

Full content:

```markdown
# QA Agent Spec

> Replace your current system prompt with this file. Fill in the project context block. Everything else is ready to use.

---

## Role

You are a risk-first QA reviewer. Your job is to find what breaks and assess how badly — not to confirm things work.

Rules you follow:
- Every finding is labeled with a risk tier (High / Medium / Low) before anything else
- You never produce unlabeled output
- You check compliance signals before you check behavior
- When you flag something High risk involving auth or financial data, you stop and escalate — you do not continue reviewing

---

## Project Context

*Fill this in before using this agent. The agent uses this to calibrate what to check.*

```
Project name: [your project name]

Services in this project:
- [service 1 name and one-sentence description]
- [service 2 name and one-sentence description]
- [add more as needed]

Where specs and ADRs live:
- [path or URL to specs]
- [path or URL to ADRs / architecture decision records]

Auth model used across this platform:
- [e.g., OAuth 2.0 with client credentials, API key per service, JWT, etc.]
- [which services require auth, which are public]

Compliance frameworks in scope:
- [ ] OSPS Baseline (OpenSSF Open Source Project Security Baseline)
- [ ] FINOS
- [ ] Other: [name]

Sensitive data this platform handles:
- [ ] PII (personally identifiable information)
- [ ] Financial data
- [ ] Compliance-regulated data
- [ ] None of the above
```

---

## Risk Tiers

Use these tiers to label every finding. No finding is delivered without a tier.

| Tier | What belongs here |
|---|---|
| **High** | Auth flaws, financial data exposure, PII leaks, external integration failures, violations of compliance rules in ADRs |
| **Medium** | Contract mismatches (behavior doesn't match spec), missing error handling, undocumented behavior that could cause problems |
| **Low** | Non-breaking inconsistencies, cosmetic API issues, minor deviations from spec that don't affect correctness or security |

---

## What to Check — In This Order

Work through these categories in priority order. Do not skip to Layer 2 (API behavior) before completing Layer 1 (compliance signals) for the service.

### 1. OSPS Baseline Compliance Signals

For each service/repo being reviewed, check:

**AC — Access Control**
- Branch protection configured?
- Workflow permissions appropriately scoped?
- Auth and authorization controls documented and enforced?

**BR — Build & Release**
- CI/CD inputs sanitized?
- Secret scanning active on this repo?
- Release artifacts signed or attested (e.g., SLSA)?
- HTTPS enforced on all release endpoints?

**DO — Documentation**
- Vulnerability disclosure process documented?
- Dependency management policy documented?
- User or integration guide exists?

**GV — Governance**
- Contribution guidelines present?
- Code review requirements documented and enforced?

**LE — Legal**
- License file present and valid?
- License consistent across all releases?

**QA — Quality Assurance**
- Dependency management automated or regularly reviewed?
- Code review required before merge?
- Binary files excluded from repo?

**SA/VM — Security Assessment & Vulnerability Management**
- Threat model documented?
- Vulnerability disclosure policy present?
- Security contact listed?
- Private vulnerability reporting enabled on this repo?

### 2. Auth and Access

- Does this service require auth? What type?
- What happens with an invalid or expired token? (Expected: 401, not 500)
- What happens with a missing required scope? (Expected: 403, not silent success or wrong data)
- Are tokens passed in headers — not URLs or query strings?
- Does rate limiting behave correctly when exceeded?

### 3. Spec / ADR Compliance

- Does the service's behavior match what its spec says?
- Does it match the decisions recorded in its ADRs?
- Are there any undocumented deviations from either?

### 4. Error Behavior

- Malformed input → 400, not 500?
- Unavailable dependency → graceful failure, not silent hang?
- Error messages useful but don't leak internals (stack traces, infra names)?
- Errors logged and findable?

### 5. FINOS / Compliance Signals

*(Only apply if your project context has these frameworks checked)*

- PII and financial data not appearing in logs or error responses?
- Sensitive operations have an audit trail?
- Compliance rules from ADRs enforced in the service?
- Financial operations have safety checks (limits, validations, boundary conditions)?

---

## Finding Format

Every flag you produce must use this format exactly. No exceptions.

```
Service: [name of the service]
Risk: High / Medium / Low
OSPS Control: [control ID if applicable, e.g. AC-01, BR-03 — leave blank if not tied to a control]
Finding: [one sentence — what is wrong]
Expected: [what the spec or ADR says should happen]
Actual: [what is happening instead]
Suggested action: fix / investigate / document as known gap
```

**Example:**

```
Service: auth-gateway
Risk: High
OSPS Control: AC-01
Finding: Branch protection is not enabled on the main branch
Expected: Per OSPS AC-01, the default branch must require pull request reviews before merge
Actual: Direct pushes to main are allowed with no review
Suggested action: fix
```

---

## Escalation Logic

| Situation | Action |
|---|---|
| High-risk finding involving auth or financial data | Stop. Report the finding. Do not continue reviewing until it is acknowledged. |
| High-risk finding not involving auth or financial data | Flag it clearly. Continue reviewing. |
| Medium or Low finding | Flag it. Continue reviewing. Do not block. |

---

## Notes for the Agent Runner

- Start with the services marked highest risk in your onboarding output (see `02-qa-onboarding.md`)
- Never skip the OSPS Baseline layer — compliance gaps are as important as behavioral ones
- If something isn't in a spec or ADR, say so explicitly in the finding — "no documented expected behavior found"
- When in doubt about risk tier, go higher
```

- [ ] **Step 2: Verify the file exists**

Run: `ls C:/Users/Aly\ Kravetz/Documents/qa-starter-kit/01-qa-agent-spec.md`
Expected: file listed

- [ ] **Step 3: Commit**

```bash
cd C:/Users/Aly\ Kravetz/Documents/qa-starter-kit
git add 01-qa-agent-spec.md
git commit -m "feat: add QA agent spec (01) — replaces AI-generated system prompt"
```

---

### Task 2: Write `02-qa-onboarding.md`

**Files:**
- Create: `02-qa-onboarding.md` (repo root)

- [ ] **Step 1: Write the file**

Full content:

```markdown
# QA Onboarding Checklist

> Use this the first time you're invited into a project that already has specs, ADRs, and code. Follow the phases in order. Do not write a single test until Phase 2 is complete.

---

## Phase 1 — Read Before You Touch Anything

*Why: You'll write wrong tests if you don't understand the decisions already made.*

- [ ] Read all ADRs — understand the WHY behind decisions, not just what was built
- [ ] Read all service specs — what each service should do, and what's explicitly out of scope
- [ ] Map the auth model — how services authenticate with each other, and how users authenticate with services
- [ ] Map sensitive data — what data flows through the platform and what's sensitive (PII, financial, compliance-regulated)
- [ ] Map service dependencies — what calls what; draw it if it helps
- [ ] Check for existing OSPS Baseline assessment results — look for a `security-insights.yml` file or PVTR output in each repo

---

## Phase 2 — Assess Risk Before Writing a Single Check

*Why: Without a risk ranking, you'll spend time on the wrong things first.*

Answer these questions for each service:

- [ ] Is this service external-facing? (Higher risk — more attack surface)
- [ ] Does this service touch financial data or compliance rules? (Highest risk in FINOS context)
- [ ] Does this service have zero tests? (Coverage gap — flag immediately)
- [ ] Does this service have no Security Insights file or OSPS Baseline coverage?
- [ ] What is the blast radius if this service fails? (What breaks downstream?)

**Output of Phase 2:** A ranked list of services, ordered by risk. This is the order you test them in.

| Rank | Service | Risk drivers | Test first? |
|---|---|---|---|
| 1 | [service name] | [e.g., external-facing, financial data, no tests] | Yes |
| 2 | [service name] | [e.g., internal only, has tests, no financial data] | After #1 |
| ... | | | |

---

## Phase 3 — Set Up Your Environment

*Why: You can't test anything reliably without knowing where to look when things go wrong.*

- [ ] Identify dev and staging environments — which one do you use for testing?
- [ ] Find test credentials and sandbox configs — where do these live?
- [ ] Find the logging stack — where do errors surface? (Loki, CloudWatch, Datadog, etc.)
- [ ] Know what can be tested in isolation vs. what requires full integration running

---

## Phase 4 — First Checks

*Why: Starting with everything at once leads to unfocused, unfinished testing.*

- [ ] Start with the top 2–3 highest-risk services from your Phase 2 ranking only
- [ ] Write every check against the spec or ADR — not your assumptions about what should happen
- [ ] Document expected vs. actual behavior explicitly, every time — even if the test passes
- [ ] Use the finding format from `01-qa-agent-spec.md` for anything that fails

---

## What Not to Do

- **Do not start with the services you're most familiar with.** Start with the highest risk.
- **Do not assume behavior.** Every check traces back to a spec or ADR.
- **Do not test everything at once.** Two services tested well beats ten tested badly.
- **Do not skip Phase 2.** A risk assessment is not optional — it is the whole point.
```

- [ ] **Step 2: Verify the file exists**

Run: `ls C:/Users/Aly\ Kravetz/Documents/qa-starter-kit/02-qa-onboarding.md`
Expected: file listed

- [ ] **Step 3: Commit**

```bash
cd C:/Users/Aly\ Kravetz/Documents/qa-starter-kit
git add 02-qa-onboarding.md
git commit -m "feat: add QA onboarding checklist (02) — 4-phase risk-first process"
```

---

### Task 3: Write `03-api-service-checks.md`

**Files:**
- Create: `03-api-service-checks.md` (repo root)

- [ ] **Step 1: Write the file**

Full content:

```markdown
# API Service Check Template

> Use this template once per service. Fill in the header, then work through both layers in order. Layer 1 (OSPS Baseline) always comes before Layer 2 (API behavior).

---

## Service Header

Fill this in before starting checks.

```
Service name: 
Repo URL: 
Spec / ADR location: 
External-facing? Yes / No
Touches financial data? Yes / No
Touches PII? Yes / No
Has existing tests? Yes / No / Partial
OSPS Baseline assessment exists? Yes / No — [link if yes]
Tester: 
Date: 
```

---

## Layer 1 — OSPS Baseline Checks

*Why this comes first: Compliance gaps are as important as behavioral bugs. A service that works correctly but has no branch protection or no vulnerability disclosure process is still a risk.*

OSPS Baseline is the Open Source Project Security Baseline from OpenSSF — 39 controls across 7 categories. Check each category for this service's repo.

### AC — Access Control

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Branch protection enabled on default branch | | |
| Pull request reviews required before merge | | |
| Workflow permissions appropriately scoped (not `write-all`) | | |
| Auth and authorization controls documented | | |
| Auth controls enforced in code, not just documented | | |

**Control IDs:** AC-01 (branch protection), AC-02 (workflow permissions)

---

### BR — Build & Release

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| CI/CD pipeline inputs sanitized | | |
| Secret scanning active on this repo | | |
| No secrets committed in history | | |
| Release artifacts signed or attested (e.g., SLSA provenance) | | |
| HTTPS enforced on all release and download endpoints | | |

**Control IDs:** BR-01 (secret scanning), BR-02 (artifact signing), BR-03 (HTTPS)

---

### DO — Documentation

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Vulnerability disclosure process documented | | |
| Dependency management policy documented | | |
| User or integration guide exists | | |

**Control IDs:** DO-01 (vulnerability disclosure docs), DO-02 (dependency policy)

---

### GV — Governance

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Contribution guidelines present (`CONTRIBUTING.md` or equivalent) | | |
| Code review requirements documented | | |
| Code review requirements enforced (not just documented) | | |

**Control IDs:** GV-01 (contribution guidelines), GV-02 (code review policy)

---

### LE — Legal

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| License file present (`LICENSE` or `LICENSE.md`) | | |
| License is OSI-approved or appropriate for FINOS context | | |
| License consistent across all releases and packages | | |

**Control IDs:** LE-01 (license present), LE-02 (license consistency)

---

### QA — Quality Assurance

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Dependency management automated (Dependabot, Renovate, etc.) or regularly reviewed | | |
| Code review required before merge (policy enforced, not just stated) | | |
| Binary files excluded from repo | | |

**Control IDs:** QA-01 (dependency management), QA-02 (code review enforcement), QA-03 (binary files)

---

### SA/VM — Security Assessment & Vulnerability Management

| Check | Pass / Fail / N/A | Notes |
|---|---|---|
| Threat model documented | | |
| Vulnerability disclosure policy present (`SECURITY.md` or equivalent) | | |
| Security contact listed (in `SECURITY.md` or repo settings) | | |
| Private vulnerability reporting enabled (GitHub: Security → Private reporting) | | |

**Control IDs:** SA-01 (threat model), VM-01 (disclosure policy), VM-02 (private reporting)

---

## Layer 2 — API / Integration Checks

*Why this comes second: Behavior checks against a service with no compliance baseline give you false confidence. Know the compliance posture before you verify behavior.*

### Auth & Access

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| Service requires auth | Documented in spec: yes / no | | |
| Invalid or expired token | 401 response | | |
| Missing required scope | 403 response (not silent success, not wrong data returned) | | |
| Tokens passed in headers | Not in URLs, not in query strings | | |
| Rate limit exceeded | Correct error response (typically 429) | | |

---

### Contract / Spec Compliance

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| Endpoint exists at documented path | Per spec | | |
| Request shape matches spec | Per spec | | |
| Response shape matches spec | Per spec | | |
| Status codes match spec | Per spec | | |
| Required fields enforced | 400 if missing | | |
| Optional fields handled correctly when absent | No crash, no wrong data | | |
| Behavior matches its ADR | Per ADR | | |

---

### Error Handling

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| Malformed input | 400, not 500 | | |
| Dependency unavailable | Graceful failure, not silent hang | | |
| Error messages don't leak internals | No stack traces, no infra names in response | | |
| Errors are logged | Findable in logging stack | | |

---

### FINOS / Compliance Signals

*Skip this section if FINOS is not in scope for this project.*

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| PII not in logs | No PII visible in log output | | |
| PII not in error responses | No PII in any error response body | | |
| Financial data not in logs or error responses | No financial data leaking | | |
| Sensitive operations have audit trail | Logged with user, timestamp, action | | |
| Compliance rules from ADRs enforced | Per ADR | | |
| Financial operations have limits/validations | Boundary conditions handled | | |

---

### Integration Points

| Check | Expected | Actual | Pass / Fail |
|---|---|---|---|
| Service calls its dependencies correctly | Per spec | | |
| Unexpected downstream response handled gracefully | Error surfaced, not swallowed | | |
| Webhooks/callbacks validated before acted on | Signature or source verified | | |
| Cross-service data matches on both sides of the integration | Same values on sender and receiver | | |

---

## Findings Log

Use this section to record anything that fails. Copy the format from `01-qa-agent-spec.md`.

```
Service: 
Risk: High / Medium / Low
OSPS Control: [ID if applicable]
Finding: 
Expected: 
Actual: 
Suggested action: fix / investigate / document as known gap
```

---

*Add more finding blocks as needed.*
```

- [ ] **Step 2: Verify the file exists**

Run: `ls C:/Users/Aly\ Kravetz/Documents/qa-starter-kit/03-api-service-checks.md`
Expected: file listed

- [ ] **Step 3: Commit**

```bash
cd C:/Users/Aly\ Kravetz/Documents/qa-starter-kit
git add 03-api-service-checks.md
git commit -m "feat: add API service check template (03) — OSPS Layer 1 + API Layer 2"
```

---

### Task 4: Update `README.md`

**Files:**
- Modify: `README.md` (repo root)

- [ ] **Step 1: Write the updated README**

Full content:

```markdown
# QA Starter Kit

A practical QA toolkit for developers working on multi-service open source platforms — especially projects in or adjacent to the FINOS ecosystem and OpenSSF OSPS Baseline.

Built for developers who are being invited into an existing project after the fact, have specs and ADRs but no QA process, and need methodology — not just more tooling.

---

## What's in this kit

| File | What it does |
|---|---|
| [`01-qa-agent-spec.md`](01-qa-agent-spec.md) | Replaces an AI-generated system prompt with a methodology-grounded QA agent spec. Fill in the project context block and use it. |
| [`02-qa-onboarding.md`](02-qa-onboarding.md) | A four-phase checklist for the first time you're invited into an existing project. Read this before writing a single test. |
| [`03-api-service-checks.md`](03-api-service-checks.md) | A per-service check template. Two layers: OSPS Baseline compliance first, then API behavior. Copy once per service. |

---

## How to use this kit

**If you have a QA agent and want to make it better:** Start with `01-qa-agent-spec.md`. Fill in the project context block at the top. Replace your current system prompt with this file.

**If you're starting QA on an existing project for the first time:** Start with `02-qa-onboarding.md`. Follow the four phases in order before writing any checks.

**When you're ready to check a specific service:** Use `03-api-service-checks.md`. Fill in the service header, work through Layer 1 (OSPS Baseline), then Layer 2 (API behavior). Log findings using the format in `01-qa-agent-spec.md`.

---

## The methodology behind this kit

This kit is grounded in the [OpenSSF OSPS Baseline](https://github.com/ossf/security-baseline) — a set of 39 security and quality controls across 7 categories that are already used in FINOS ecosystem projects.

**Why compliance checks come first:** A service that works correctly but has no branch protection, no vulnerability disclosure policy, and no secret scanning is still a risk. OSPS Baseline checks catch the things behavioral testing misses.

**Why risk ranking comes before test writing:** In a multi-service platform, you can't test everything. The onboarding checklist helps you figure out which services matter most before you spend time on the wrong ones.

**Why every finding has a format:** Unlabeled QA output creates noise. Every finding in this kit includes a risk tier, an OSPS control ID (where applicable), and a clear expected vs. actual statement — so findings are actionable and traceable.

---

## Compliance context

The OSPS Baseline categories used in this kit:

| Category | What it covers |
|---|---|
| AC — Access Control | Branch protection, workflow permissions, auth controls |
| BR — Build & Release | CI/CD security, secret scanning, artifact signing |
| DO — Documentation | Vulnerability disclosure, dependency policy, integration guides |
| GV — Governance | Contribution guidelines, code review requirements |
| LE — Legal | License presence and consistency |
| QA — Quality Assurance | Dependency management, code review enforcement |
| SA/VM — Security Assessment & Vulnerability Management | Threat models, disclosure policies, private reporting |

---

## Who built this

Built by a QA engineer working in identity and account services. Shared because the question "do you have a starter kit for QA first-time setup?" deserves a real answer.
```

- [ ] **Step 2: Commit**

```bash
cd C:/Users/Aly\ Kravetz/Documents/qa-starter-kit
git add README.md
git commit -m "docs: update README with kit overview and usage guide"
```

---

### Task 5: Push to GitHub

- [ ] **Step 1: Verify all files are committed**

```bash
cd C:/Users/Aly\ Kravetz/Documents/qa-starter-kit
git log --oneline -5
```

Expected: 4 commits (01 agent spec, 02 onboarding, 03 service checks, README update)

- [ ] **Step 2: Push**

```bash
cd C:/Users/Aly\ Kravetz/Documents/qa-starter-kit
git push origin main
```

Expected: All commits pushed to https://github.com/SaltyAly/qa-starter-kit
